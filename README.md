# 🧠 RAG-Based Conversational System with LLaMA, FAISS, and Multilingual TTS Integration

## Overview
A complete Retrieval-Augmented Generation (RAG) pipeline designed for humanoid conversational systems, integrating sentence-level chunking, FAISS-based semantic retrieval, reranking, memory, multi-hop reasoning, web search fallback, and multilingual speech I/O.
---
## 🔹 Pipeline Overview
### 1. Document Chunking
- Converted raw documents into semantic chunks.
- Used sentence-based chunking (better context flow than character chunking).
- Split text using NLTK sentence splitter.
- Created chunks of 3 sentences, with 1-sentence overlap for continuity.

---

### 2. Embedding Generation and Vector Storage
- Downloaded and initialized a **sentence embedding model**.
- Converted each text chunk into an **embedding vector**.
- Stored embedding vectors in a **FAISS vector database** for fast similarity search.
- 
---

- The LLM handles query understanding, document synthesis, and final answer generation.

---

### 3. Retrieval and Cross-Encoder Reranking
- For every user query:
- Converted the query text into an **embedding vector**.
- Retrieved the **top 10 most similar chunks** from FAISS.
- Applied **cross-encoder reranking** to filter out irrelevant chunks:
  - Helps discard false positives where no chunk is meaningfully relevant.
- The top reranked contexts are provided to the LLM for response synthesis.

---

### 4. Conversational Memory
- Implemented **session-based memory** for contextual continuity.
- The system remembers past questions and answers within a session.
- Memory is stored in a local database for retrieval across turns.

---

### 5. Web Search Integration
- Integrated **DuckDuckGo Search API** for fallback retrieval.
- Triggered when no relevant chunk is found in FAISS or the confidence is low.
- Uses DuckDuckGo’s free API (with rate limits) to fetch real-time web information.

---

### 6. Complex Query Decomposition
- Detected when queries are **complex or multi-part**.
- Decomposed them into **sub-questions**.
- Each sub-question is processed through the RAG pipeline individually.
- Aggregated and stored the sub-answers in memory for holistic reasoning and reuse.

---

### 7. Multilingual Voice Interaction (TTS + ASR)
- Integrated **speech-to-text (ASR)** and **text-to-speech (TTS)** for humanoid interaction.
- Process:
1. Converts **audio input → text**.
2. **Detects input language** automatically.
3. **Translates** non-English text to English.
4. Feeds the translated question into the RAG system.
5. **Translates response back** to the original language.
6. Generates **audio output** so the user can hear the answer.

---

## ⚙️ Key Features
- Sentence-level semantic chunking with overlap.
- FAISS-based vector similarity retrieval.
- Cross-encoder reranking for precise answer selection.
- LLaMA 2 for reasoning and conversation.
- Contextual memory across sessions.
- Web fallback with DuckDuckGo Search.
- Support for multilingual queries and voice-based interaction.

---

## 🧩 Tech Stack
| Component | Library / Tool |
|------------|----------------|
| Text Splitting | `NLTK` |
| Embeddings | Sentence Transformers / Hugging Face model |
| Vector Store | `FAISS` |
| LLM | `LlamaCpp` (LLaMA 2 - 7B) |
| Memory | LangChain conversational memory |
| Web Search | DuckDuckGo Search API |
| Speech & Translation | ASR, Google Translate API / any TTS engine |

---
## 🏗️ Architecture Diagram

The following flow illustrates the entire pipeline of the system:

A[📄 Documents] --> B[Sentence-based Chunking (NLTK)]
B --> C[Embedding Model]
C --> D[(FAISS Vector DB)]

subgraph Retrieval
    E1[User Query (Text/Voice)]
    E1 --> E2[ASR → Language Detection → Translation]
    E2 --> E3[Query Embedding]
    E3 --> E4[FAISS Retrieval]
    E4 --> E5[Cross-Encoder Reranker]
    E5 --> E6[Top-k Relevant Chunks]
end

D --> E4
E6 --> F[LLaMA LLM]
F --> G1[Answer Generation]
F --> G2[Web Search (DuckDuckGo)]
G1 --> H[Translation Back to Original Language]
G2 --> H
H --> I[TTS → Audio Response 🎧]
E1 --> M[(Memory DB)]
F --> M
G1 --> M

style Retrieval fill:#F5F5FF,stroke:#666,stroke-width:1px
style D fill:#FFF8E1,stroke:#E0C400,stroke-width:1px
style F fill:#E3F2FD,stroke:#2196F3,stroke-width:1px
style M fill:#F0FFF4,stroke:#4CAF50,stroke-width:1px
style I fill:#E8EAF6,stroke:#3F51B5,stroke-width:1px
