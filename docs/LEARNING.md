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