<div align="center">

# 🧠 Qdrant RAG Chat App

### Retrieval-Augmented Generation chat powered by CrewAI agents and Qdrant vector search

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue?logo=python&logoColor=white)](https://www.python.org/)
[![CrewAI](https://img.shields.io/badge/CrewAI-Agents-orange)](https://www.crewai.com/)
[![Qdrant](https://img.shields.io/badge/Qdrant-Vector%20DB-red?logo=qdrant&logoColor=white)](https://qdrant.tech/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

[Features](#-features) • [Quick Start](#-quick-start) • [Architecture](#-architecture) • [Configuration](#-configuration) • [Usage](#-usage) • [Contributing](#-contributing)

</div>

---

## ✨ Overview

**Qdrant RAG Chat App** is a context-aware chat application that combines **CrewAI's multi-agent orchestration** with **Qdrant's vector search** to deliver accurate, grounded answers from your own documents. Instead of relying purely on an LLM's parametric memory, the app retrieves the most relevant chunks from your knowledge base and lets specialized agents reason over them before responding.

> Ask questions in natural language. Get answers grounded in your own data, with sources.

## 🚀 Features

- **🔍 Semantic Search** — Documents are embedded and indexed in Qdrant for fast, accurate similarity search
- **🤖 Multi-Agent Pipeline** — CrewAI agents handle retrieval, reasoning, and response generation as discrete, composable roles
- **💬 Chat Interface** — Clean conversational UI with persistent chat history
- **📄 Flexible Ingestion** — Load PDFs, Markdown, text files, or custom data sources into the vector store
- **⚙️ Configurable Models** — Swap embedding models and LLM providers via environment variables
- **🐳 Docker-Ready** — Spin up Qdrant and the app with a single command
- **📊 Source Attribution** — Responses reference the retrieved chunks they were grounded in

## 🏗️ Architecture

```
┌─────────────┐      ┌──────────────┐      ┌─────────────────┐
│   User      │ ───> │  Chat Layer  │ ───> │  CrewAI Agents   │
│  (Query)    │      │ (Streamlit/  │      │  - Retriever     │
└─────────────┘      │   FastAPI)   │      │  - Synthesizer   │
                      └──────────────┘      │  - Responder     │
                                             └────────┬─────────┘
                                                       │
                                                       ▼
                                             ┌──────────────────┐
                                             │  Qdrant Vector DB │
                                             │  (Embeddings +    │
                                             │   Document Store) │
                                             └──────────────────┘
```

1. **Ingestion** — Documents are chunked, embedded, and upserted into a Qdrant collection.
2. **Retrieval** — On each query, the relevant chunks are fetched via vector similarity search.
3. **Agentic Reasoning** — CrewAI agents synthesize the retrieved context into a coherent, accurate answer.
4. **Response** — The final answer is streamed back to the user, optionally with cited sources.

## 📦 Prerequisites

- Python 3.10+
- [Docker](https://www.docker.com/) (recommended, for running Qdrant locally) or a [Qdrant Cloud](https://cloud.qdrant.io/) account
- An API key for your chosen LLM provider (OpenAI, Anthropic, etc.)

## ⚡ Quick Start

### 1. Clone the repository

```bash
git clone https://github.com/crewAIInc/qdrant_rag_chat_app.git
cd qdrant_rag_chat_app
```

### 2. Set up a virtual environment

```bash
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Start Qdrant

```bash
docker run -p 6333:6333 -p 6334:6334 \
  -v $(pwd)/qdrant_storage:/qdrant/storage:z \
  qdrant/qdrant
```

### 5. Configure environment variables

Copy the example file and fill in your credentials:

```bash
cp .env.example .env
```

```env
# Qdrant
QDRANT_URL=http://localhost:6333
QDRANT_API_KEY=

# LLM Provider
OPENAI_API_KEY=your_openai_key_here
# or
ANTHROPIC_API_KEY=your_anthropic_key_here

# Embeddings
EMBEDDING_MODEL=text-embedding-3-small
COLLECTION_NAME=rag_chat_collection
```

### 6. Ingest your documents

```bash
python ingest.py --source ./data
```

### 7. Launch the app

```bash
python app.py
```

Then open your browser to **`http://localhost:8501`** (or the port specified in your config).

## 🔧 Configuration

| Variable | Description | Default |
|---|---|---|
| `QDRANT_URL` | URL of your Qdrant instance | `http://localhost:6333` |
| `QDRANT_API_KEY` | API key for Qdrant Cloud (leave blank for local) | — |
| `COLLECTION_NAME` | Name of the Qdrant collection to use | `rag_chat_collection` |
| `EMBEDDING_MODEL` | Embedding model used for document vectors | `text-embedding-3-small` |
| `LLM_MODEL` | Chat/completion model used by CrewAI agents | `gpt-4o-mini` |
| `CHUNK_SIZE` | Document chunk size for splitting | `1000` |
| `CHUNK_OVERLAP` | Overlap between chunks | `200` |

## 💻 Usage

**Ask a question via the chat UI:**

```
You: What were the key findings in the Q3 report?
Assistant: According to the Q3 report, revenue grew 18% YoY,
driven primarily by... [Source: q3_report.pdf, chunk 4]
```

**Re-index after adding new documents:**

```bash
python ingest.py --source ./data --rebuild
```

**Run in CLI mode (no UI):**

```bash
python app.py --cli
```

## 🗂️ Project Structure

```
qdrant_rag_chat_app/
├── agents/              # CrewAI agent and task definitions
├── data/                 # Source documents to be indexed
├── ingest.py             # Document loading, chunking, embedding
├── app.py                # Chat application entrypoint
├── requirements.txt
├── .env.example
└── README.md
```

## 🛣️ Roadmap

- [ ] Support for additional vector stores
- [ ] Multi-turn conversation memory
- [ ] Hybrid (keyword + vector) search
- [ ] Streaming token-by-token responses
- [ ] Evaluation suite for retrieval quality

## 🤝 Contributing

Contributions are welcome! Please open an issue to discuss major changes before submitting a pull request.

1. Fork the repo
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the [MIT License](LICENSE).

## 🙏 Acknowledgments

- [CrewAI](https://www.crewai.com/) for the multi-agent orchestration framework
- [Qdrant](https://qdrant.tech/) for the vector search engine

---

<div align="center">

Made with ❤️ using CrewAI and Qdrant

</div>
