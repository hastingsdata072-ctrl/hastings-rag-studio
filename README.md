# 🧠 Hastings RAG Studio

> *Your documents. Your knowledge. Your intelligence.*

A professional **Document Intelligence and Agentic RAG platform** built by Hastings AI. Upload documents, ask questions, and receive evidence-grounded answers with full citations.

---

## Prerequisites

| Tool | Version |
|---|---|
| Python | 3.11 or later |
| Docker Desktop | Any recent version |
| VS Code | Any recent version |

---

## Setup (Windows PowerShell)

### 1. Start the PostgreSQL + pgvector container

```powershell
docker run --name hastings-pg `
  -e POSTGRES_PASSWORD=YOUR_PASSWORD `
  -e POSTGRES_DB=hastings_rag `
  -p 5432:5432 `
  -d pgvector/pgvector:pg16
```

Replace `YOUR_PASSWORD` with a strong password. Keep this container running whenever you use the app.

To start it again after a reboot:
```powershell
docker start hastings-pg
```

### 2. Open the project folder

```powershell
cd "c:\Users\user\Downloads\Hastings studion\hastings-rag-studio"
```

### 3. Create and activate a virtual environment

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

### 4. Install dependencies

```powershell
pip install -r requirements.txt
```

### 5. Configure environment variables

```powershell
Copy-Item .env.example .env
```

Open `.env` and fill in:
- `GROQ_API_KEY` — your Groq API key (starts with `gsk_`)
- `DATABASE_URL` — replace `YOUR_PASSWORD` with the password you used in step 1

### 6. Initialise the database

```powershell
python scripts/init_db.py
```

This creates the `knowledge_bases`, `documents`, and `chunks` tables, plus the HNSW vector index. Safe to run multiple times.

### 7. Run the acceptance tests

```powershell
python tests/test_db_connection.py
```

All checks should show `PASS ✅`. Fix any failures before continuing.

### 8. Start the application

```powershell
streamlit run app.py
```

Open [http://localhost:8501](http://localhost:8501) in your browser.

---

## Project Structure

```
hastings-rag-studio/
├── app.py                   # Streamlit entry point
├── requirements.txt
├── .env                     # never committed — your local secrets
├── .env.example             # template with placeholder values
├── .gitignore
├── README.md
├── scripts/
│   └── init_db.py           # one-time DB initialisation
├── src/
│   ├── config.py            # loads and validates all settings
│   ├── db/
│   │   ├── connection.py    # psycopg v3 connection + pool
│   │   └── schema.py        # idempotent DDL
│   ├── health.py            # live component health checks
│   ├── ingestion/           # extractors, cleaning, chunking (Milestone 2)
│   ├── embeddings/          # embedding model wrapper (Milestone 2)
│   ├── retrieval/           # vector search, hybrid, rerank (Milestone 2+)
│   ├── generation/          # prompts, Groq client, citations (Milestone 2)
│   └── evaluation/          # metrics (Milestone 3+)
├── tests/
│   └── test_db_connection.py
├── components/              # reusable Streamlit UI pieces
└── assets/
    └── uploads/             # local file storage (gitignored)
```

---

## Milestone Checklist

- [x] **Milestone 1** — Foundation: skeleton, config, DB, health check, status page
- [ ] **Milestone 2** — Core RAG: upload, extract, chunk, embed, retrieve, generate, citations
- [ ] **Milestone 3** — Advanced RAG: hybrid retrieval, reranking, query rewriting, evaluation
- [ ] **Milestone 4** — Product layer: RAG Intelligence Lab, dashboard, Docker, AWS deployment

---

## Technology Stack

| Component | Technology |
|---|---|
| UI | Streamlit |
| LLM | Groq API |
| Embeddings | sentence-transformers (`all-MiniLM-L6-v2`) |
| Vector database | PostgreSQL 16 + pgvector (HNSW, cosine) |
| PostgreSQL driver | psycopg v3 |
| PDF extraction | PyMuPDF |
| DOCX extraction | python-docx |
| Config | python-dotenv |
