# Build a Retrieval-Augmented Generation (RAG) System — End-to-End Challenge

**Goal (high level):**  
Design and implement a working **Retrieval-Augmented Generation (RAG)** system from scratch that answers user queries using an external knowledge base. This exercise evaluates your **system design**, **engineering**, and **AI integration** skills — think production-readiness, modularity, observability, and faithfulness of answers.

---

## Deliverables (what to submit)
1. A working codebase (Git repo) with clear README and setup steps. Prefer a Docker/docker-compose-based run if possible.  
2. A short system design doc (1–2 pages) describing architecture, trade-offs, scaling plan, and security concerns.  
3. Demo: short screen recording (or hosted URL) showing queries and outputs.  
4. Automated tests (unit + integration where possible) and a small evaluation script.  
5. A `results/` folder with: sample queries, model outputs, retrieved docs, and evaluation metrics.

---

## Requirements (must-have)
1. **Data / Knowledge Base**
   - Ingest at least **50–100 documents** (PDFs, Markdown, or plain text). You may use any public dataset (FAQ pages, product manuals, research abstracts, or scraped docs). Provide a script to ingest these docs.
   - Perform document preprocessing: text extraction, cleaning (remove boilerplate), segmentation/chunking (with chunk size + overlap).

2. **Embedding storage**
   - Compute embeddings for chunks using any embedder (OpenAI, Cohere, Sentence-Transformers, etc.) or an open-source model.  
   - Store embeddings in a vector DB (FAISS, Milvus, Weaviate, Pinecone, Qdrant). Provide a fallback local/FAISS option.

3. **Retriever**
   - Implement semantic retrieval returning **top-k** chunks. Support configurable `k`.  
   - Support optional hybrid retrieval (BM25/keyword + semantic) — **bonus**.

4. **Generator (LLM)**
   - Use an LLM to synthesize answers from retrieved chunks. You can call a hosted API (OpenAI, Anthropic) or a local open model (Llama-style).  
   - Implement **prompting strategy**: include retrieved context and clear instruction to the model (e.g., answer using sources only; cite sources inline).

5. **Answer Composition**
   - Return: (a) final answer text, (b) list of retrieved chunks/documents with metadata (title/source/score/offset), and (c) model prompt used (for reproducibility).

6. **Faithfulness & Safety**
   - The generator must not hallucinate beyond retrieved context. Include a mechanism (prompt + post-processing) to ensure citations and source grounding.
   - If the model cannot answer from sources, it should respond: *"I don't know — couldn't find supporting documents."*

7. **Interface**
   - Expose a minimal interface: either a simple web UI, REST API, or CLI. Example flow:
     ```
     POST /query { "q": "What is X?" } -> { "answer": "...", "sources": [...], "confidence": 0.72 }
     ```

8. **Evaluation**
   - Provide an evaluation harness that runs a set of queries and computes:
     - **Retrieval relevance** (Precision@k or Recall@k using labeled ground truth if available).
     - **Answer faithfulness**: fraction of answer content that can be traced to retrieved sources (manual or heuristics-based).  
     - **Automatic metrics** (optional): ROUGE / BLEU vs. reference answers, exact match / F1 for short QA.
   - Provide ≥20 test queries (with expected sources or reference answers if possible).

---

## Acceptance Criteria (how we'll judge)
- The system runs end-to-end locally (or via provided hosted demo).  
- Answers include retrieved sources and the model refrains from unsupported claims.  
- Clean, modular code with docs and tests.  
- Demonstrated evaluation with metrics and example failures/limitations discussed.
- Export your AI chats and add them in a `/ai_chats/` folder (to help us evaluate your AI usage capabilities)

---

## Scoring Rubric (suggested)
- **System Design & Architecture (25%)** — modularity, scaling plan, fault tolerance.  
- **Retrieval Quality (20%)** — good chunking, embedding choice, and retrieval accuracy.  
- **LLM Integration & Prompting (20%)** — effective prompts, citation behaviour, minimal hallucination.  
- **Engineering & UX (15%)** — reproducibility, README, API/UI.  
- **Evaluation & Metrics (10%)** — thoughtful evaluation and honest analysis.  
- **Creativity & Extras (10%)** — caching, reranker, query rewriting, streaming, monitoring, access control.

---

## Suggested Tech Stack (pick what you prefer)
- Backend: Python (FastAPI/Flask), Node.js (Express).  
- Embeddings: OpenAI embeddings, Cohere, or SBERT.  
- Vector DB: FAISS (local), Qdrant, Pinecone, Milvus, Weaviate.  
- LLMs: OpenAI GPT family, Anthropic, or local Llama/Falcon via API.  
- Frontend (optional): Simple React app or plain HTML + JS.  
- Containerization: Docker + docker-compose.

---

## Implementation Hints & Tips
- **Chunking:** 500–1,000 tokens per chunk with 10–20% overlap works well for long docs.  
- **Prompts:** Use an instruction to the LLM like: *“Use ONLY the following sources. If the answer is not present, say you cannot answer.”* Include source labels (DocA#3).  
- **Reranking:** After retrieval, rerank by cross-encoder or by using the generator to score candidate chunks — improves precision.  
- **Caching:** Cache embeddings and top-k results for repeated queries.  
- **Observability:** Log queries, retrieved IDs, LLM outputs, latencies, and errors. Build simple dashboards or logs.  
- **Failure modes:** Show examples where the system fails (missing docs, ambiguous queries, hallucination) and propose mitigations.

---

## Example Test Cases (include similar in your test set)
1. **Fact lookup:** “What are the main steps of X protocol described in Document Y?” — expected: bullet list with doc citation.  
2. **Multi-source synthesis:** “Summarize the differences between A and B across the corpus.” — expected: concise comparison citing sources.  
3. **Unanswerable:** “When was the company Z founded?” (not present) — expected: “I don’t know — no source found.”  
4. **Contradiction check:** Query that matches two conflicting sources — expected: present both sources and indicate conflict.

---

## Stretch Goals (worth bonus points)
- Implement query rewriting / clarification (turning long questions into focused retrieval queries).  
- Implement hybrid retrieval (BM25 + semantic).  
- Add a reranker (cross-encoder).  
- Provide streaming LLM responses.  
- Role-based access control, rate limiting, and API keys.  
- Add automated synthetic evaluation for faithfulness (e.g., QA over retrieved contexts to check answer support).

---

## What to include in the README
- How to run locally (commands, environment vars).  
- Architecture diagram (ASCII or image).  
- How to ingest documents and reindex.  
- How to run tests and evaluation.  
- Known limitations and next steps.

---

## Bonus ask (optional)
If you want, provide a "buggy starter repo" version where core components exist but contain intentional issues (bad chunking, missing citation plumbing, a prompt that encourages hallucination). This turns the task into both a **build** and **debug** exercise.

---

### Final note
Aim for a **minimum viable, reproducible pipeline** that clearly demonstrates design thinking and AI integration. Focus on reproducibility, evidence-grounded answers, and honest evaluation. Good luck!
