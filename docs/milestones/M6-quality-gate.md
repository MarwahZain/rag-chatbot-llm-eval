# M6: Quality gate in GitHub Actions

| | |
| --- | --- |
| Status | Not started |
| Branch | `ci/m6-quality-gate` |
| Time | 2 to 3 hours |

## Goal

No change to the prompt, model or documents reaches `main` without passing the quality gate.

## Concepts

- **Quality gate:** the build fails when any metric falls below its threshold, the same as a coverage or test-pass gate.
- **Why every change needs it:** in an LLM app, a one-word prompt edit, a new model version or an updated FAQ can change answers without any code change. The gate turns "seems fine" into evidence.
- **Two jobs, cheap first:** deterministic tests (free, fast) run first; the DeepEval job (API calls, slower) only runs if they pass.
- **Infrastructure vs quality failures:** retries handle 503 and 429 so a red build means the answers got worse, not that the provider was busy.

## Tasks

- [ ] GitHub repo secret `GEMINI_API_KEY` (Settings > Secrets and variables > Actions)
- [ ] `.github/workflows/eval.yml` on `push` and `pull_request` to `main`
- [ ] Job 1 `deterministic`: checkout, Python 3.12, pip cache, `pytest -m "not llm_judge"` (or the M3 markers)
- [ ] Job 2 `llm-eval`: `needs: deterministic`, runs DeepEval tests with the secret
- [ ] Cache pip packages and the Chroma embedding model (`~/.cache/chroma`)
- [ ] Upload test results (JUnit XML) as an artifact; write a short job summary with scores
- [ ] `concurrency` group so a new push cancels the old run (saves free-tier quota)
- [ ] Branch protection on `main`: the eval workflow must pass before merging
- [ ] **Prove the gate:** push a branch with a broken prompt (the invented-fee defect from M4), open a PR, watch it fail, then fix it. Screenshot both runs
- [ ] README badge for the workflow
- [ ] Optional: Dependabot for pip and GitHub Actions

## Workflow skeleton

Check the latest major versions of the actions when you write it (your Playwright repo is a good reference).

```yaml
name: LLM eval

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

concurrency:
  group: eval-${{ github.ref }}
  cancel-in-progress: true

jobs:
  deterministic:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-python@v6
        with:
          python-version: "3.12"
          cache: pip
      - run: pip install -r requirements.txt
      - run: pytest -m "retrieval or answers or negative" --junitxml=results-deterministic.xml
        env:
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
          GEMINI_MODEL: gemini-3.8-flash
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: deterministic-results
          path: results-deterministic.xml

  llm-eval:
    needs: deterministic
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: actions/setup-python@v6
        with:
          python-version: "3.12"
          cache: pip
      - run: pip install -r requirements.txt
      - run: pytest tests/test_rag_metrics.py --junitxml=results-eval.xml
        env:
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
          GEMINI_MODEL: gemini-3.8-flash
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: eval-results
          path: results-eval.xml
```

## Definition of done

- Green run on `main`, red run on the broken-prompt PR, both visible in the Actions tab
- `main` protected: merging needs a green gate
- Badge in the README

## PR template

**Title:** `ci: LLM quality gate on every push and PR (M6)`

```markdown
## What
- `eval.yml`: deterministic job, then DeepEval job (only if the first passes)
- Thresholds from `tests/thresholds.py` fail the build
- Results uploaded as artifacts, job summary with scores
- Branch protection on main

## Proof
- PR #N with an injected defect (invented fee): gate failed on faithfulness ❌
- Same PR after revert: gate passed ✅

## Cost and limits
- Free tier; concurrency cancels outdated runs; retries absorb 503/429
```

## Interview points

- "Prompt, model and document changes are releases: the gate runs on all of them."
- "I proved the gate by opening a PR with a deliberate regression and watching it fail."
- "Cheap deterministic checks fail first; the paid judge only runs when needed."

## Notes and findings

-
