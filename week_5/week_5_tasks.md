# Week 5 — Retrieval: Embeddings, Vector Search, Chunking, RAG Evals

**Theme:** build a retrieval-augmented generation (RAG) pipeline properly — and,
in keeping with this path's philosophy, **measure it** with retrieval-specific evals
instead of eyeballing answers.

**Why this matters:** grounding a model in private knowledge is the most common
enterprise LLM pattern, and the difference between good and bad RAG is almost never
the model — it's chunking, retrieval quality, and evaluation. "We use RAG" is a
sentence; *"our retriever hits the right chunk in the top-3 92% of the time and our
answers are faithful to retrieved context 96% of the time"* is an engineering claim.

---

## Learning goals

By the end of this week you can:

- Choose and justify a chunking strategy (fixed, recursive, semantic; size + overlap).
- Build embedding + vector search (pgvector) and explain cosine similarity honestly.
- Evaluate retrieval (hit rate, MRR) **separately** from generation
  (faithfulness, answer relevancy).
- Explain when RAG beats fine-tuning beats long-context prompting — and mixes thereof.

---

## Tasks

### 1. Corpus + chunking lab (~2h)

- [ ] Pick a public, meaty corpus (e.g. a project's documentation — Kubernetes docs,
      Postgres docs, or any OSS handbook). 50–200 pages is plenty.
- [ ] Implement two chunking strategies (e.g. fixed-size with overlap vs
      heading/recursive-structure aware). Keep both; you'll compare them with evals,
      not opinions.

### 2. Embedding + vector store (~2h)

- [ ] Use an embedding model from your provider (Together serves BGE-family models;
      any provider's embedding endpoint works the same way).
- [ ] Store vectors in **Postgres + pgvector** (Docker one-liner) — the boring,
      production-realistic choice. Index with cosine distance.
- [ ] Build `search(query, k)` returning chunks + scores. Sanity-check with 5 queries.

### 3. The RAG pipeline (~1.5h)

- [ ] Wire retrieval into a generation prompt with clear rules: answer only from
      context, cite chunk IDs, say "not in the docs" when it isn't.
- [ ] Expose it as a `search_docs` **function tool** on an agent (reuse Week 4
      patterns) rather than a hardcoded pre-retrieval step. Note in `NOTES.md` when
      agentic retrieval (model decides when/what to search, can search twice) beats
      fixed retrieve-then-answer.

### 4. RAG evaluation (~3h) — the core of the week

Build a 25-question golden dataset against your corpus: factual lookups, multi-chunk
synthesis questions, ambiguous questions, and **5 questions the corpus cannot answer**
(the "should abstain" set).

- [ ] **Retrieval metrics** (no LLM needed): label which chunk(s) answer each
      question; measure hit@k and MRR for both chunking strategies. Declare a winner
      with numbers.
- [ ] **Generation metrics** (LLM-as-judge, Week 3 skills): faithfulness (is every
      claim supported by retrieved context?) and answer relevancy. DeepEval has both
      built in: https://docs.confident-ai.com/
- [ ] **Abstention check:** does the system correctly refuse the 5 unanswerable
      questions, or does it hallucinate? Report the number.
- [ ] Write the results table in the README: chunking A vs B × (hit@3, MRR,
      faithfulness, abstention).

### 5. The decision framework (~1h)

- [ ] Write a one-page `rag_vs_finetune_vs_prompt.md`: for each approach — what it's
      good at, cost profile, update latency (how fast new knowledge lands), and a
      concrete example use case. Include hybrid patterns (RAG + fine-tuned model;
      long-context + retrieval reranking).

---

## Resources

- pgvector: https://github.com/pgvector/pgvector
- OpenAI Cookbook RAG + evaluation notebooks: https://github.com/openai/openai-cookbook
- DeepEval RAG metrics: https://docs.confident-ai.com/
- Chunking strategies overview (Pinecone's guide is solid):
  https://www.pinecone.io/learn/chunking-strategies/
- Reranking intro (why top-k retrieval often isn't enough):
  https://www.pinecone.io/learn/series/rag/rerankers/

---

## Deliverable

An evaluated RAG pipeline: corpus, two chunking strategies, pgvector search, agentic
`search_docs` tool, 25-question golden set, and a README results table comparing
strategies on retrieval + generation + abstention metrics.

## Self-check

1. Your answers are bad. How do you tell whether retrieval or generation is at fault?
2. Why measure abstention explicitly? What failure mode does it catch?
3. A stakeholder asks "why not just fine-tune on our docs?" — give the honest
   three-sentence answer.
