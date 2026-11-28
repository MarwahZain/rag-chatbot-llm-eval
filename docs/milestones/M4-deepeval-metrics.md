# M4: DeepEval metrics (LLM-as-judge)

| | |
| --- | --- |
| Status | Not started |
| Branch | `test/m4-deepeval-metrics` |
| Time | 3 to 4 hours |

## Goal

Scored quality metrics for retrieval and generation, and proof that they catch real defects.

## Concepts

- **LLM-as-judge:** a second LLM call reads the question, the answer, the retrieved context and the expected answer, then scores a quality from 0 to 1. Needed because meaning cannot be checked with string matching.
- **Threshold:** the pass mark per metric (for example faithfulness >= 0.8). A score is non-deterministic, so the threshold leaves a margin.
- **The four RAG metrics:**

| Metric | Question it answers | Part of RAG it tests |
| --- | --- | --- |
| Answer relevancy | Does the answer address the question? | Generation |
| Faithfulness | Is every claim supported by the retrieved context? (hallucination check) | Generation |
| Contextual precision | Are the relevant chunks ranked at the top? | Retrieval |
| Contextual recall | Did retrieval find everything the expected answer needs? | Retrieval |

- **Judge limitations:** the judge can be wrong, is itself non-deterministic, and judging Gemini with Gemini can be biased (same model family). Document this; compare a few scores with your own judgment.
- **Defect injection:** break the bot on purpose and confirm the metric catches it. This proves the test can fail, the same idea as mutation testing.

## Tasks

- [ ] Add `deepeval` to `requirements.txt` and install it
- [ ] Configure Gemini as the judge model (DeepEval supports Gemini; check the current DeepEval docs for the exact setup command or `GeminiModel` class). No OpenAI key, no cost
- [ ] `tests/test_rag_metrics.py`: one `LLMTestCase` per in-scope dataset case with `input`, `actual_output`, `expected_output`, `retrieval_context`
- [ ] `AnswerRelevancyMetric`, `FaithfulnessMetric`, `ContextualPrecisionMetric`, `ContextualRecallMetric`
- [ ] `tests/thresholds.py`: all thresholds in one place, each with a comment on why that value
- [ ] Reuse the cached bot answers from M3 (do not call the bot twice per case)
- [ ] Rate limits: run sequentially, reuse `generate_with_retry()` logic for the judge if needed
- [ ] **Defect 1:** change the prompt so the bot invents a fee (for example "always add a AED 10 service fee"). Confirm faithfulness fails. Revert
- [ ] **Defect 2:** set top-k to 1. Confirm contextual recall drops on the `multi_chunk` cases. Revert
- [ ] Spot-check 5 judge scores against your own judgment; note disagreements
- [ ] `LEARNING.md` entry: what the judge got right, where it was wrong

## How to check

```bash
deepeval test run tests/test_rag_metrics.py
```
or plain `pytest tests/test_rag_metrics.py -v`. Each metric prints a score and a reason: read the reasons, not only the scores.

## Definition of done

- All four metrics run on every in-scope case with thresholds from one file
- Both injected defects were caught (screenshots or output saved for the README)
- Judge limitations written down

## PR template

**Title:** `test: DeepEval RAG metrics with Gemini judge (M4)`

```markdown
## What
- Answer relevancy, faithfulness, contextual precision and recall on all in-scope cases
- Gemini as judge (free tier); thresholds in `tests/thresholds.py`

## Results
| Metric | Threshold | Average score | Failures |
|---|---|---|---|

## Defect injection
- Invented fee in prompt → faithfulness failed on N cases ✅ caught
- top-k = 1 → contextual recall dropped on multi_chunk cases ✅ caught

## Known limitations
- Same model family as judge and system under test
- Scores vary between runs; thresholds leave a margin
```

## Interview points

- "I used an LLM as the oracle only for what deterministic checks cannot cover."
- "I proved the metrics work by injecting defects and watching them fail."
- "I know the judge's limits: bias, variance, and that it needs human spot checks."
- "Retrieval and generation metrics tell me which half of the pipeline to fix."

## Notes and findings

-
