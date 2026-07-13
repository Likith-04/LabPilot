# LabPilot - AI-Powered PDF Chat using RAG & Multi-Agent Pipeline

LabPilot is a full-stack AI-powered Retrieval-Augmented Generation (RAG) application that enables users to upload PDF documents and interact with them through natural language conversations.

The application extracts document content, creates semantic vector embeddings using FAISS, retrieves the most relevant context, and generates accurate, grounded responses using a multi-agent AI pipeline powered by modern Large Language Models.

LabPilot is built with a scalable architecture consisting of a React + TypeScript frontend and a FastAPI backend, supporting multiple AI providers including Groq, OpenRouter, Google Gemini, Hugging Face, and OpenAI.

---

# ✨ Features

- 📄 Upload and chat with PDF documents
- 🤖 Multi-Agent Retrieval-Augmented Generation (RAG) pipeline
- 🔍 Semantic document search using FAISS Vector Database
- ⚡ Streaming AI responses using Server-Sent Events (SSE)
- 🧠 Automatic document chunking and embedding generation
- 📚 Source-aware AI responses with document citations
- 🔄 Multiple AI provider support (Groq, OpenRouter, Gemini, Hugging Face & OpenAI)
- 💾 Session-based document isolation using UUIDs
- 🚀 Local sentence-transformers embedding fallback
- 🛡️ Built-in upload and chat rate limiting
- 📜 Browser-based chat history persistence
- 🐳 Docker-ready backend deployment
- ☁️ Railway backend deployment
- ▲ Vercel frontend deployment
- 📱 Responsive modern UI

---

# 🛠 Tech Stack

## Frontend

- React 18
- TypeScript
- Vite
- Tailwind CSS
- React Router
- Framer Motion
- Radix UI
- React Markdown
- Lucide React
- Sentry (Optional)

## Backend

- FastAPI
- Python
- Uvicorn
- LangChain
- FAISS
- sentence-transformers
- PyPDF
- Pydantic
- HTTPX
- AIOHTTP
- Docker

## AI & Machine Learning

- Retrieval-Augmented Generation (RAG)
- FAISS Vector Search
- Sentence Transformers
- Groq
- OpenRouter
- Google Gemini
- Hugging Face
- OpenAI

---

# 📁 Project Structure

```text
.
├── README.md
├── package.json
├── labpilot.code-workspace
├── frontend/
│   ├── package.json
│   ├── vite.config.ts
│   ├── vercel.json
│   └── src/
│       ├── components/
│       ├── hooks/
│       ├── lib/
│       ├── pages/
│       └── types/
├── backend/
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── pyproject.toml
│   ├── app/
│   │   ├── agents/
│   │   ├── models/
│   │   ├── routes/
│   │   ├── services/
│   │   ├── config.py
│   │   └── main.py
│   └── tests/
└── docs/
```

---

# 🏗 Architecture

```text
                +-----------------------+
                |   React Frontend      |
                |  (Vite + TypeScript)  |
                +-----------+-----------+
                            |
                            |
                            ▼
              Upload PDF / Ask Question
                            |
                            ▼
                 FastAPI Backend API
                            |
         +------------------+------------------+
         |                                     |
         ▼                                     ▼
 PDF Processing                     Multi-Agent Pipeline
         |                                     |
         ▼                                     ▼
 Text Chunking                     Query Understanding
         |                                     |
         ▼                                     ▼
 Embedding Generation               Context Retrieval
         |                                     |
         +------------+-------------------------+
                      |
                      ▼
              FAISS Vector Store
                      |
                      ▼
          Relevant Context Retrieved
                      |
                      ▼
             Large Language Model
   (Groq / OpenRouter / Gemini / OpenAI)
                      |
                      ▼
          Context-Aware AI Response
                      |
                      ▼
             React Chat Interface
```

---

# 🔌 API Endpoints

Most document and chat endpoints require the `X-Chat-Session-Id` header containing a UUID v4.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | `/` | Backend Status |
| GET | `/health` | Health Check |
| GET | `/models` | Available Models |
| GET | `/pipeline-info` | Pipeline Information |
| GET | `/runtime-summary` | Runtime Summary |
| GET | `/status` | Session Status |
| POST | `/upload` | Upload & Index PDF |
| POST | `/ask` | Chat with PDF |
| POST | `/ask/stream` | Streaming Chat |
| POST | `/api/oversight` | Optional Sentry Tunnel |

Example:

```bash
curl -X POST "http://127.0.0.1:8000/ask" \
-H "Content-Type: application/json" \
-H "X-Chat-Session-Id: 11111111-2222-4333-8444-555555555555" \
-d '{"question":"Summarize this PDF","include_sources":true}'
```

---

# ⚙️ Environment Variables

## Backend

Create `backend/.env`

```env
PORT=8000

CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173

DEFAULT_PROVIDER=groq
DEFAULT_MODEL=llama-3.3-70b-versatile

GROQ_API_KEY=
OPENROUTER_API_KEY=
OPENROUTER_API_BASE=https://openrouter.ai/api/v1
GOOGLE_API_KEY=
HF_API_KEY=
OPENAI_DIRECT_API_KEY=

FAISS_PERSIST_DIR=faiss_index
MAX_VECTOR_SESSIONS=64
FAISS_SESSION_MAX_AGE_DAYS=3

RATE_LIMIT_UPLOAD_PER_MINUTE=8
RATE_LIMIT_ASK_PER_MINUTE=90

SENTRY_DSN=
SENTRY_ENVIRONMENT=local
```

## Frontend

Create `frontend/.env`

```env
VITE_API_BASE_URL=http://localhost:8000
VITE_DEV_PROXY_TARGET=http://127.0.0.1:8000
VITE_APP_ENV=local
VITE_SENTRY_DSN=
VITE_SENTRY_TRACES_RATE=0
```

Production

```env
VITE_API_BASE_URL=https://your-backend-domain.com
```

---

# 💻 Local Development

## 1. Clone Repository

```bash
git clone https://github.com/YOUR_USERNAME/LabPilot.git

cd LabPilot
```

---

## 2. Install Frontend

```bash
cd frontend

npm install

npm run dev
```

Frontend runs on

```
http://localhost:5173
```

---

## 3. Start Backend

```bash
cd backend

python -m venv .venv

source .venv/bin/activate

pip install -r requirements.txt

uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Backend

```
http://127.0.0.1:8000
```

Swagger

```
http://127.0.0.1:8000/docs
```

---

# ✅ Quality Checks

## Frontend

```bash
cd frontend

npm run typecheck

npm run lint

npm run build
```

## Backend

```bash
cd backend

pip install -r requirements.txt -r requirements-dev.txt

ruff check app

mypy app

python -m unittest discover -s tests -p "test_*.py"
```

---

# 🚀 Deployment

## Backend (Railway)

- Root Directory: `backend`
- Dockerfile: `Dockerfile`
- Container Port: `3000`
- Health Check: `/health`

Environment Variables

```env
PORT=3000

DEFAULT_PROVIDER=groq
DEFAULT_MODEL=llama-3.3-70b-versatile

GROQ_API_KEY=
OPENROUTER_API_KEY=
OPENROUTER_API_BASE=https://openrouter.ai/api/v1

FAISS_PERSIST_DIR=faiss_index

MAX_VECTOR_SESSIONS=64

FAISS_SESSION_MAX_AGE_DAYS=3

RATE_LIMIT_UPLOAD_PER_MINUTE=8

RATE_LIMIT_ASK_PER_MINUTE=90

CORS_ORIGINS=https://your-frontend-domain.vercel.app
```

Optional persistent storage mount

```
/app/faiss_index
```

---

## Frontend (Vercel)

Settings

```
Framework Preset : Vite

Root Directory : frontend

Install Command : npm install

Build Command : npm run build

Output Directory : dist
```

Environment Variable

```env
VITE_API_BASE_URL=https://your-backend-domain.up.railway.app
```

---

# 🛠 Troubleshooting

- Add your deployed frontend URL to `CORS_ORIGINS`.
- Ensure `VITE_API_BASE_URL` points to your deployed backend.
- Use the same `X-Chat-Session-Id` for upload and chat requests.
- Verify provider API keys if AI responses fail.
- Mount `/app/faiss_index` if you need persistent vector storage.
- Redeploy after changing environment variables.

---

# 🔮 Future Improvements

- Multi-PDF support
- Hybrid Search (Keyword + Semantic Search)
- OCR support for scanned PDFs
- Authentication & User Accounts
- Conversation Memory
- Cloud Object Storage
- Admin Dashboard
- Analytics & Usage Monitoring
- PDF Annotation Support

---

# 🌐 Live Demo

**Frontend:** https://lab-pilot-capbl.vercel.app

**Backend API:** https://labpilot-production.up.railway.app/docs

---

# 📸 Screenshots

- Home Page
- PDF Upload
- Chat Interface
- Source Citations
- Streaming Responses
- Mobile Responsive View

---

## ⭐ If you found this project useful, consider giving it a star on GitHub!
