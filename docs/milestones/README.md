# Milestones

One document per milestone, with everything needed to complete it without the chat history: goal, concepts, tasks, definition of done, how to check, branch, PR summary template and interview points.

The short checklist for all milestones is in [`../RAG-EVAL-ROADMAP.md`](../RAG-EVAL-ROADMAP.md). These documents are the detailed version.

| Milestone | Document | Branch | Status |
| --- | --- | --- | --- |
| M0 | [Bootstrap](M0-bootstrap.md) | `first-branch` | Done |
| M1 | [RAG chatbot](M1-rag-chatbot.md) | `first-branch` | Merged (PR #1), manual checks TC-02 to TC-05 open |
| M2 | [Test dataset](M2-test-dataset.md) | `test/m2-golden-dataset` | In progress |
| M3 | [Deterministic checks](M3-deterministic-checks.md) | `test/m3-deterministic-checks` | Not started |
| M4 | [DeepEval metrics](M4-deepeval-metrics.md) | `test/m4-deepeval-metrics` | Not started |
| M5 | [Negative and security cases](M5-security-cases.md) | `test/m5-security-cases` | Not started |
| M6 | [Quality gate in CI](M6-quality-gate.md) | `ci/m6-quality-gate` | Not started |
| M7 | [README and showcase](M7-readme.md) | `docs/m7-readme` | Not started |
| M8 | [Interview prep](M8-interview-prep.md) | none (notes, not code) | Not started |

## How to use these documents

1. Create the branch from an up-to-date `main`.
2. Work through the task list, ticking boxes as you go.
3. Check the definition of done before opening the PR.
4. Use the PR template at the bottom of each document.
5. Add findings to the "Notes and findings" section: they become README and interview material.
