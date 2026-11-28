Question: "What is the fee for an international transfer?"
     │
     ▼
1. RETRIEVE  → search the FAQ documents, pick the 2-3 most relevant chunks
     │
     ▼
2. AUGMENT   → build a prompt: "Answer using ONLY this context: [chunks] Question: ..."
     │
     ▼
3. GENERATE  → the LLM writes the answer
     │
     ▼
Answer: "International transfers cost AED 25 per transaction."

----
Why this matters for testing

We test the RAG pipeline, not just "the chatbot," because each step can fail on its own:

Step	What can go wrong	Metric we'll use
Retrieve	Picked the wrong FAQ section	Contextual precision / recall
Generate	Answer invents facts that aren't in the documents	Faithfulness (hallucination)
Generate	Answer is correct but doesn't address the question	Answer relevancy
Whole system	Answers questions it shouldn't, or follows injected instructions	Negative cases

---
When a RAG answer is wrong, a good tester asks "did retrieval fail, or did generation fail?" Testing each step separately tells you which one to fix. It works like separating API tests from UI tests so you know which layer broke.

LLM apps, RAG systems and AI agents. This one project covers the first two. Agents (an LLM that decides which tools to call) would be a possible Phase 2 later.

**Why these documents look like this (test design):**
 every answer has specific, checkable facts (AED 25, 2.5%, 30 minutes), so a hallucinated number is easy to catch. Some topics overlap across files on purpose (fees show up in accounts, cards and transfers), so retrieval has to pick the right chunk and can't rely on one obvious keyword. You designed test data for testability, just as you would for a banking regression suite.

 ## 🎓 Concepts from today (for interviews)
**Chunking:** documents are split into small pieces so retrieval returns only the relevant part, not a whole file. Chunk size is a design decision that affects quality, and something you can test.
**Embeddings:** Chroma turns each chunk and each question into a list of numbers that represent meaning. Similar meaning gives similar numbers, so "lost my card" matches "block a stolen card" even with different words.
**Top-k = 3:** how many chunks go to the LLM. Too few and you can miss the answer. Too many and you add noise that can confuse the answer.
**Temperature 0:** makes the output more consistent, but not fully deterministic. The same question can still be worded differently on another run. That's why we'll test with scores and thresholds instead of exact string matches.
**Returning contexts along with the answer:** this is testability by design. DeepEval needs to see what was retrieved to judge faithfulness and retrieval quality. A test lead asks for this hook early, just like asking developers for test IDs on UI elements.

## A good exploratory test. 
You've found a category the plan didn't have: a mixed question, half in scope and half out of scope. 
Real customers write like this all the time.

**What should the bot do?**
Decide the expected result before looking at the answer, as with any test case:
| Part	| Expected|
|-----|-----|
|"I lost my card"	|✅ Answer from the FAQ: block it in the app under Cards > Block card, or call 800 0000 000|
|"What is the capital of France"	|❌ Don't answer "Paris". Say it can't help with that, or ignore it|

**From a banking point of view**, the _worst outcome_ is the bot gives only the **fallback** answer. Then a customer with a lost card abroad gets no help at all. That's a **real customer-harm risk**, _worse than saying "Paris"_.

**Why this may expose a design gap**

Your system prompt says: "If the context does not contain the answer, reply exactly: [fallback]". It doesn't say what to do when the context contains part of the answer, so the model has to guess. It may:

- answer the card part and skip France ✅
- answer both, including "Paris" ❌ (used outside knowledge)
- give only the fallback ❌ (unhelpful, since a lost card is urgent)

Each run might even pick differently. _That's non-determinism at work_.

**What did the bot answer?**  Then:

Log it in MANUAL-TESTS.md as TC-05: mixed scope, with the expected result above and PASS or FAIL.
Don't change the prompt yet. In M2 this goes into the dataset as its own category, and if it fails, fixing rule 2 later becomes a nice before-and-after story: "I found a gap in the scope rule with a mixed question, changed the prompt, and the eval suite proved the fix."