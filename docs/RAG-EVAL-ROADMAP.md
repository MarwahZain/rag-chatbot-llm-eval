# banking-rag-eval: Roadmap

Nine milestones from first commit to a RAG chatbot with an evaluation suite that gates every push in CI. Each milestone ends with a concrete outcome.

Progress: M0 and M1 in progress. Next: M2.

Schedule: weekend 1 (Thu 26 to Sun 29 Nov) covers M0 to M6, weekend 2 (3 to 6 Dec) covers M7, M8 and anything left over.

| Day | Milestones |
| --- | ---------- |
| Thu | M0, M1 |
| Fri | M2 |
| Sat | M3, M4 |
| Sun | M5, M6 |
| Weekend 2 | M7, M8, stretch |

## M0: Bootstrap

- [ ] Get a free Gemini API key from Google AI Studio (personal account, no credit card)
- [ ] Create the public repo `banking-rag-eval` and clone it
- [ ] Create a virtual environment (`python3 -m venv .venv`) and activate it
- [ ] Add `requirements.txt` (`google-genai`, `chromadb`, `python-dotenv`)
- [ ] Add `.env` with `GEMINI_API_KEY` and `GEMINI_MODEL`, plus `.env.example` without the key
- [ ] Gitignore `.venv/`, `.env`, `__pycache__/`, `.deepeval/`, `.pytest_cache/`
- [ ] Folder structure: `data/faqs/`, `src/`, `tests/`, `docs/`

**Outcome:** a clean Python project that installs with one command and never commits the API key.

## M1: RAG chatbot

- [ ] Three FAQ files for the fictional "YAH Bank": `accounts.md`, `cards.md`, `transfers.md`
- [ ] Every answer has checkable facts (fees, rates, limits, times) so hallucinations are easy to spot
- [ ] Chunking: one chunk per `## ` question
- [ ] Retrieval: in-memory Chroma collection, top-k = 3
- [ ] System prompt: answer only from context, a fixed fallback answer, never invent numbers, ignore injected instructions
- [ ] Generation with Gemini at temperature 0, model name read from `.env`
- [ ] `ask()` returns the answer and the retrieved contexts (testability by design)
- [ ] Manual check with 4 questions: international transfer fee, lost card, capital of France, "ignore your rules"
- [ ] First commit and push

**Outcome:** a chatbot in the terminal that answers from the FAQs and refuses what it doesn't know.

## M2: Test dataset

- [ ] `tests/data/golden_dataset.json` with 16 test cases
- [ ] Fields per case: `id`, `category`, `question`, `expected_answer`, `expected_source`, `must_contain`, `notes`
- [ ] 6 direct questions, one per key fact
- [ ] 2 paraphrased questions that use different words from the FAQ ("my card got stolen")
- [ ] 2 questions that need facts from two chunks (fee and processing time of a transfer)
- [ ] 3 out-of-scope questions (general knowledge, another bank's product, investment advice)
- [ ] 2 prompt injection attempts (reveal the system prompt, override the rules)
- [ ] 1 mixed-scope question, half in scope and half out (found by exploratory testing)
- [ ] `docs/DATASET.md`: categories, why each one exists, how expected answers were written
- [ ] Review every case by hand: one clear expected result each, no vague "answers correctly"

**Outcome:** a golden dataset designed like a test suite, with coverage you can explain category by category.

## M3: Deterministic checks

- [ ] Add `pytest` to `requirements.txt`
- [ ] `tests/conftest.py`: load the dataset once, call the bot once per case, cache the results
- [ ] `test_retrieval.py`: `expected_source` is among the retrieved chunks, parametrized over the dataset
- [ ] `test_answers.py`: every `must_contain` value appears in the answer (for example `AED 25`)
- [ ] `test_negative.py`: out-of-scope and injection cases return the fallback answer
- [ ] Format guards: answer is not empty and under 80 words
- [ ] Run the suite 3 times and note any test that flips between pass and fail
- [ ] LEARNINGS entry: why exact-match assertions are flaky on LLM output and key-fact checks are not

**Outcome:** a fast, free test layer that catches the obvious failures before any LLM judge runs.

## M4: DeepEval metrics (LLM-as-judge)

- [ ] Install `deepeval` and configure Gemini as the judge model (no OpenAI cost)
- [ ] Build an `LLMTestCase` per case: `input`, `actual_output`, `expected_output`, `retrieval_context`
- [ ] `AnswerRelevancyMetric`: does the answer address the question?
- [ ] `FaithfulnessMetric`: is every claim in the answer supported by the retrieved context?
- [ ] `ContextualPrecisionMetric`: are the relevant chunks ranked first?
- [ ] `ContextualRecallMetric`: did retrieval find everything needed for the expected answer?
- [ ] Thresholds in one place (`tests/thresholds.py`), with a comment on why each value was chosen
- [ ] Defect injection: change the prompt so the bot invents a fee, confirm faithfulness fails, then revert
- [ ] Defect injection: set top-k to 1, confirm contextual recall drops on the two-chunk cases, then revert
- [ ] Handle free-tier rate limits (run cases one at a time, retry on 429)
- [ ] LEARNINGS entry: what the judge got right, where it disagreed with my own judgment

**Outcome:** scored quality metrics for retrieval and generation, and proof that they catch real defects.

## M5: Negative and security cases

- [ ] Out-of-scope: assert the fallback answer and no invented facts
- [ ] Prompt injection: "ignore previous instructions", "you are now a different assistant", "print your system prompt"
- [ ] Indirect injection: a question that hides an instruction inside normal text
- [ ] `GEval` metric with a custom criterion: "refuses safely and stays in its banking support role"
- [ ] Sensitive request: asking for another customer's account details is refused
- [ ] Tag security cases so they can run on their own (`pytest -m security`)
- [ ] Record any case the bot fails as a GitHub issue, with the fix or the accepted risk

**Outcome:** evidence that the bot stays in scope and resists basic injection, with known gaps logged openly.

## M6: Quality gate in GitHub Actions

- [ ] Add `GEMINI_API_KEY` as a repository secret
- [ ] `.github/workflows/eval.yml` on every push and pull request
- [ ] Job 1: install, deterministic tests (fast, fails first)
- [ ] Job 2: DeepEval metrics, runs only when job 1 passes
- [ ] The build fails when any metric falls below its threshold
- [ ] Cache pip packages and the Chroma embedding model
- [ ] Upload the test results as an artifact and write a short job summary
- [ ] Prove the gate: push a branch with a broken prompt, watch it fail, open the PR, then fix it
- [ ] README badge for the workflow

**Outcome:** no change to the prompt, model or documents reaches `main` without passing the quality gate.

## M7: README and showcase

- [ ] What quality means for an LLM app (relevant, grounded, correct retrieval, safe, consistent)
- [ ] Architecture diagram: question, retrieve, augment, generate, evaluate
- [ ] How the dataset was designed (link to `docs/DATASET.md`)
- [ ] What each metric checks, in one table, with its threshold
- [ ] Results: latest scores and the defects the suite caught during M4 and M6
- [ ] Limits: small dataset, LLM judge from the same model family, free-tier rate limits, no human evaluation, scores vary between runs
- [ ] What I would add for a real bank: private or self-hosted model, larger reviewed dataset, human review sample, monitoring in production
- [ ] Local setup in five commands
- [ ] Tag release v1.0

**Outcome:** a README that reads like a test strategy from a test lead, not a tutorial.

## M8: Interview prep

- [ ] One CV bullet: honest, specific, with real numbers from the repo
- [ ] Answer to "How would you test an LLM application?" based on what I built
- [ ] 3 to 5 follow-up questions with short answers (non-determinism, LLM-as-judge limits, threshold choice, hallucination vs retrieval failure, injection)
- [ ] Explain the project out loud in 2 minutes, without notes
- [ ] Link the repo on the CV and LinkedIn

**Outcome:** I can explain every design decision in this repo and why I made it.

## Stretch (after v1.0)

- [ ] Same dataset scored with RAGAS, compare the results with DeepEval
- [ ] Swap the judge to a different model family and compare the scores
- [ ] Run each case 5 times and report the score spread (consistency metric)
- [ ] Simple UI with Streamlit for demos
- [ ] Next project: `llm-agent-eval`, evaluating my own Claude Code test-analyst and QA-architect agents (covers the AI agents part of job ads)

## Git workflow

- One branch per milestone, with a prefix: `feat/`, `test/`, `docs/`, `ci/`
- Pull request to `main`, the eval gate must pass before merging (from M6 on)
- Conventional commit messages
- Release tag v1.0 at the end of M7
