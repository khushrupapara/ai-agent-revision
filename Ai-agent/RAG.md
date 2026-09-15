# RAG (Retrieval-Augmented Generation)

---

## 1. What is RAG?

- **Problem it solves:** LLMs (e.g., GPT) only know what they were trained on. They can **hallucinate** or give outdated answers because they can't access an organization's internal/current data.
- **RAG's fix:** Combines a **retriever** (fetches relevant external documents) with a **generator** (LLM that writes the answer) so responses are grounded in real, up-to-date information — not just memorized training data.
- **Analogy:** A brilliant scholar (LLM) who now has access to a live, searchable library (retriever) instead of relying only on memory.
- **Origin:** Introduced by **Facebook AI Research (Meta)**.

---

## 2. How RAG Works — Simple Version

1. **AI's brain** – LLM has pre-trained general knowledge.
2. **Retriever** – When it doesn't know something, it searches an external knowledge base for relevant documents.
3. **Combine** – Retrieved content is merged with the original query.
4. **Generate** – LLM produces an answer using both its trained knowledge + retrieved facts.

---

## 3. How RAG Works — Technical Version

| Component | Function |
|---|---|
| **Query Encoding** | User query converted into a vector/numerical representation (via models like BERT/GPT) capturing semantic meaning |
| **Retriever (DPR – Dense Passage Retriever)** | Encodes query + candidate documents into dense vectors (bi-encoder, BERT-based); finds top-K most similar/relevant documents |
| **Generator** | A seq2seq model (e.g., **BART**) uses retrieved docs + original query to generate the final output |
| **Integration/Marginalization** | Retrieved docs are treated as latent variables and combined into the output |

**Two RAG variants:**
- **RAG-Sequence** – uses the *same* retrieved document for the whole output sequence (marginalizes over document choices)
- **RAG-Token** – can use *different* documents per generated token → more flexible, more diverse output

**Training & Decoding:**
- **Training:** Retriever + generator trained end-to-end (no explicit labels for "correct" documents); minimizes negative marginal log-likelihood via stochastic gradient descent
- **Decoding:** RAG-Sequence uses beam search across documents; RAG-Token does token-wise marginalization

**Retrieval search method:** Often uses **Maximum Inner Product Search (MIPS)** to find nearest/most relevant vectors quickly.

---

## 4. Key Components Summary (quick recall)

- Query Encoder → turns question into vector
- Retriever (DPR) → finds top-K relevant docs
- Generator (BART/seq2seq) → writes final answer
- Non-parametric memory = external knowledge base (can be updated without retraining)
- Parametric memory = the LLM's own trained weights

---

## 5. RAG vs Fine-Tuning

| | **RAG better when...** | **Fine-tuning better when...** |
|---|---|---|
| Data freshness | Info changes often (news, finance, live support) | Not needed — knowledge is stable |
| Domain breadth | Multiple domains/data types (manuals, FAQs, forums) | Deep expertise in ONE narrow domain |
| Cost | Cheaper — no retraining needed | More expensive (requires retraining) |
| Model integrity | Avoids **catastrophic forgetting** (model doesn't lose old knowledge) | Risk of forgetting prior knowledge |
| Precision/control | — | Better for consistent style, tone, or strict output guidelines |
| Reasoning | Good for facts, less for reasoning | Better for structured reasoning tasks |

**One-line takeaway:** RAG = *external, dynamic knowledge*. Fine-tuning = *internal, specialized skill*.

---

## 6. Key Use Cases of RAG

1. **Contract Review (Legal)** – extract clauses, compare terms, check compliance
2. **Document Search & Data Extraction** – quickly find specific info across large document sets
3. **Document Redlining / Due Diligence** – detect changes/discrepancies between document versions
4. **Document Comparison** – compare multiple versions of a document for consistency
5. **Finance / Portfolio Analysis** – pull up-to-date market data & financial reports for decision-making
6. **Customer Support** – chatbots give accurate, current answers
7. **Healthcare** – access latest research/clinical guidelines for diagnostics

---

## 7. When Should a Business Implement RAG?

Use RAG when accuracy + real-time data matter:
- Legal document comparison (case law, statutes)
- Business/market data analysis
- Financial report generation
- Medical record analysis (latest research + patient data)

---

## 8. RAG Implementation Steps (Custom Build)

**Step 1 – Data Preparation**
- Collect relevant documents (reports, contracts, etc.)
- Clean & preprocess into machine-readable format

**Step 2 – Embedding Generation**
- Convert text chunks into embeddings (numerical/semantic representation)
- Use a **chunking strategy** (recursive or semantic chunking) to split documents while preserving context

**Step 3 – Indexing**
- Store embeddings in a **vector database** (e.g., Pinecone, FAISS)
- Add **metadata** (doc type, date, etc.) to improve retrieval accuracy

**Step 4 – Retrieval Configuration**
- Convert user query into an embedding
- Run **similarity search** against the vector database
- Apply **re-ranking algorithms** to prioritize most relevant chunks

**Step 5 – Integration with LLM**
- Combine retrieved chunks + original query → augmented prompt
- Feed into LLM → generate final grounded response

---

## 9. RAG Software/Tools (Quick Comparison)

| Tool | Known For |
|---|---|
| **Cohere (Command R+)** | 128k-token context window, multilingual, in-line citations to reduce hallucination |
| **Perplexity AI** | Combines real-time web retrieval + generation |
| **Glean** | Enterprise search with Knowledge Graph; strong security & personalization |
| **Aporia** | Not pure RAG — provides guardrails/monitoring for AI outputs |
| **Pinecone** | Vector database; real-time updates, metadata filtering, serverless scaling |
| **AWS RAG** | Scalable cloud-based RAG integration |

*(Note: Original source also promoted "V7 Go" repeatedly as a product — omitted here as it's vendor marketing, not a general RAG concept.)*

---

## 10. Core Takeaways (Exam-Ready Summary)

- RAG = **Retrieval** (finds facts) + **Generation** (writes answer using those facts)
- Fixes hallucination & outdated-knowledge problems in LLMs
- Two memory types: **parametric** (model weights) + **non-parametric** (external database, easily updatable)
- Main technical building blocks: **Query Encoder → Retriever (DPR) → Generator (BART)**
- Two generation modes: **RAG-Sequence** vs **RAG-Token**
- RAG ≠ Fine-tuning: RAG for *fresh/broad* knowledge, fine-tuning for *deep/consistent* domain skill
- Implementation pipeline: **Data prep → Embeddings → Vector DB indexing → Retrieval/ranking → LLM generation**
- Common use cases: legal, finance, healthcare, customer support, document comparison/search
