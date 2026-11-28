# M8: Interview prep

| | |
| --- | --- |
| Status | Not started |
| Branch | none (notes, not code) |
| Time | 2 hours |

## Goal

Explain every design decision in this repo, and why, without notes.

## Tasks

- [ ] **One CV bullet:** honest, specific, with real numbers from the repo (cases, metrics, defects caught). No exaggeration: "built", "designed", "evaluated", not "led a team" or "deployed to production"
- [ ] **Answer to "How would you test an LLM application?"** in about 1 minute, based on what you built: define quality, golden dataset, deterministic checks first, LLM judge for meaning, security cases, CI gate, limits
- [ ] **Follow-up questions with short answers:**
  - [ ] How do you test something non-deterministic?
  - [ ] Why trust an LLM to judge another LLM? What are the limits?
  - [ ] How did you choose the thresholds?
  - [ ] How do you tell a hallucination from a retrieval failure?
  - [ ] How do you test for prompt injection?
  - [ ] What would change for a real bank (data privacy, scale, monitoring)?
  - [ ] What happens when the provider changes the model?
- [ ] Explain the project out loud in 2 minutes, without notes (record yourself once)
- [ ] Link the repo on the CV and LinkedIn; optional LinkedIn post about one finding

## Material to draw on

- Findings in each milestone's "Notes and findings" section
- `docs/MANUAL-TESTS.md` run log
- Defect injection results (M4) and the gate proof (M6)

## Definition of done

- CV bullet final and on the CV
- 1-minute answer and 5+ follow-ups rehearsed out loud
- Repo linked publicly

## Notes and findings

-
