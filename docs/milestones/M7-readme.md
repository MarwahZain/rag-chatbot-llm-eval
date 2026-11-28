# M7: README and showcase

| | |
| --- | --- |
| Status | Not started |
| Branch | `docs/m7-readme` |
| Time | 2 to 3 hours |

## Goal

A README that reads like a test strategy written by a test lead, not a tutorial. It is the first thing a recruiter or interviewer opens.

## README structure

- [ ] **Title, one-line summary, CI badge**
- [ ] **What this is:** a RAG chatbot for a fictional bank, and the evaluation suite that gates it
- [ ] **What quality means for an LLM app:** relevant, grounded (faithful), correct retrieval, safe and in scope, consistent across runs
- [ ] **Architecture diagram:** question → retrieve → augment → generate → evaluate (Mermaid renders on GitHub)
- [ ] **Test strategy:** the layers (deterministic → LLM judge → security → CI gate) and why cheap checks run first
- [ ] **Dataset design:** categories and counts, link to `docs/DATASET.md`
- [ ] **Metrics table:** metric, what it checks, which part of RAG, threshold
- [ ] **Results:** latest scores, and the defects the suite caught (injected defects from M4 and M6, real findings such as TC-05)
- [ ] **Findings from building it:** retired model (404), 503 under load (infra vs quality), partial-scope prompt gap
- [ ] **Limitations:** small dataset (16 cases), judge from the same model family, free-tier rate limits, no human evaluation, scores vary between runs
- [ ] **What I would add for a real bank:** private or self-hosted model (data residency), larger reviewed dataset, human review sample, red-teaming, production monitoring and drift checks
- [ ] **Run it locally:** five commands
- [ ] **Project structure:** folder map
- [ ] Tag release `v1.0`

## Definition of done

- Someone who has never seen the repo understands the approach in 2 minutes
- Every claim in the README is backed by something in the repo (a test, a run, a screenshot)
- `v1.0` tagged

## PR template

**Title:** `docs: README with test strategy, results and limits (M7)`

```markdown
## What
- README rewritten as a test strategy: quality definition, layers, dataset, metrics, results, limits
- Architecture diagram (Mermaid)
- Release v1.0

## Why
The repo is a portfolio piece for AI Test Lead roles; the README is the first thing reviewers read.
```

## Interview points

- The README is your interview script: walk through it top to bottom.

## Notes and findings

-
