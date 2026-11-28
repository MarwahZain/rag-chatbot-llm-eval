# M3: Deterministic checks

| | |
| --- | --- |
| Status | Not started |
| Branch | `test/m3-deterministic-checks` |
| Time | 2 to 3 hours |

## Goal

A fast, free pytest layer that catches the obvious failures before any LLM judge runs.

## Concepts

- **Deterministic vs non-deterministic checks:** retrieval is deterministic (same question, same chunks), so test it like an API. The LLM's wording is not, so check only hard facts (`AED 25` present, `Paris` absent), never the full sentence.
- **Cheap checks first:** deterministic checks cost nothing and never flake. The LLM judge (M4) costs API calls and has its own variance, so it only checks what is left: meaning and relevance.
- **Caching bot answers:** each case calls the bot once per test run, and every test reuses that answer. This saves API calls and keeps all tests looking at the same answer.

## Tasks

- [ ] Add `pytest` to `requirements.txt` and install it
- [ ] `tests/conftest.py`: load the dataset once; a session-scoped fixture that calls `ask()` once per case and caches the result
- [ ] `tests/test_retrieval.py`: for in-scope cases, the expected source chunk is among the retrieved contexts (parametrized over the dataset, test IDs = case IDs)
- [ ] `tests/test_answers.py`: every `must_contain` value is in the answer; no `must_not_contain` value is in it
- [ ] `tests/test_negative.py`: `out_of_scope` and `injection` cases return the fallback answer
- [ ] Format guards: answer not empty, under 80 words
- [ ] `pytest.ini` or `pyproject.toml`: markers `retrieval`, `answers`, `negative`
- [ ] Run the suite 3 times; note any test that flips between pass and fail
- [ ] `LEARNING.md` entry: why exact-match assertions are flaky on LLM output and key-fact checks are not

## How to check

```bash
pytest -v
pytest -m retrieval -v      # retrieval only: no LLM calls needed if you test retrieve() directly
```
Expect test names like `test_must_contain[DS-01]` so a failure points straight at a case.

## Definition of done

- All tests run green, or failures are explained and logged as findings
- Retrieval tests do not call the LLM at all
- 3 runs compared, flaky tests noted

## PR template

**Title:** `test: deterministic checks for retrieval, facts and fallback (M3)`

```markdown
## What
- pytest suite parametrized over the golden dataset
- Retrieval: expected chunk in top-k (no LLM calls)
- Answers: must_contain / must_not_contain facts
- Negative: fallback for out-of-scope and injection
- Format guards: non-empty, under 80 words

## Results
- X passed, Y failed (list failures and why)
- Stability over 3 runs: (flaky tests, if any)

## Why
A free, fast layer that fails first, before the LLM judge in M4.
```

## Interview points

- "I test retrieval like an API, deterministically, without the LLM."
- "For generated text I assert on facts, not wording."
- "I ran the suite three times to measure flakiness instead of assuming stability."

## Notes and findings

-
