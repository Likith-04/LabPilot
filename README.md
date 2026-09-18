# RAG PDF Chat - Multi-Agent Pipeline

A full-stack RAG application for chatting with uploaded PDF documents. The app lets a user upload a PDF, builds a session-specific FAISS vector index, retrieves relevant document chunks, and generates grounded answers through a FastAPI multi-agent pipeline.

The project is split into a React/Vite frontend and a Python/FastAPI backend.

## Features

- PDF upload and text extraction
- Per-browser session isolation with `X-Chat-Session-Id`
- FAISS vector search for retrieval
- Multi-step answer pipeline using backend agents
- Streaming responses with Server-Sent Events
- Non-streaming JSON chat endpoint
- Optional source snippets
- Provider fallback support for OpenRouter, Groq, Gemini, Hugging Face, and OpenAI
- Local embedding fallback with `sentence-transformers`
- Rate limits for upload and chat endpoints
- Frontend chat history stored locally in the browser
- Docker-ready backend deployment
- Vercel-ready Vite frontend deployment

## Tech Stack

### Frontend

- React 18
- TypeScript
- Vite
- Tailwind CSS
- React Router
- Framer Motion
- Radix UI
- Sentry browser SDK, optional

### Backend

- FastAPI
- Uvicorn
- Pydantic Settings
- LangChain
- FAISS CPU
- sentence-transformers
- pypdf
- httpx / aiohttp
- Docker

## Project Structure

```text
.
├── README.md
├── package.json
├── rag-pdf-chat.code-workspace
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

## Architecture

```text
React frontend
  ├── Creates or reuses browser session ID
  ├── Uploads PDF with X-Chat-Session-Id
  ├── Sends questions to /ask or /ask/stream
  └── Stores chat history locally

FastAPI backend
  ├── Validates session ID
  ├── Extracts PDF text
  ├── Splits text into chunks
  ├── Builds FAISS index per session
  ├── Retrieves relevant chunks
  ├── Runs the multi-agent answer pipeline
  └── Returns grounded answers and optional sources
```

## API Endpoints

Most document and chat endpoints require the `X-Chat-Session-Id` header with a UUID v4 value.

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/` | Basic backend status |
| `GET` | `/health` | Health check |
| `GET` | `/models` | Available model/provider list |
| `GET` | `/pipeline-info` | Pipeline details |
| `GET` | `/runtime-summary` | Runtime usage summary |
| `GET` | `/status` | Session PDF/index status |
| `POST` | `/upload` | Upload and index a PDF |
| `POST` | `/ask` | Ask a question with JSON response |
| `POST` | `/ask/stream` | Ask a question with SSE streaming |
| `POST` | `/api/oversight` | Optional Sentry tunnel |

Example:

```bash
curl -X POST "http://127.0.0.1:8000/ask" \
  -H "Content-Type: application/json" \
  -H "X-Chat-Session-Id: 11111111-2222-4333-8444-555555555555" \
  -d '{"question":"Summarize this PDF","include_sources":true}'
```

## Environment Variables

Use `backend/.env.example` as the backend template. Create frontend env values manually or through the deployment dashboard.

### Backend

Create `backend/.env` from `backend/.env.example` for local development, or set the same variables in your deployment platform:

```env
# App
PORT=8000
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173

# Default model/provider
DEFAULT_PROVIDER=groq
DEFAULT_MODEL=llama-3.3-70b-versatile

# Provider keys - set at least one usable LLM provider
GROQ_API_KEY=
OPENROUTER_API_KEY=
OPENROUTER_API_BASE=https://openrouter.ai/api/v1
GOOGLE_API_KEY=
HF_API_KEY=
OPENAI_DIRECT_API_KEY=

# Retrieval and storage
FAISS_PERSIST_DIR=faiss_index
MAX_VECTOR_SESSIONS=64
FAISS_SESSION_MAX_AGE_DAYS=3

# Rate limits
RATE_LIMIT_UPLOAD_PER_MINUTE=8
RATE_LIMIT_ASK_PER_MINUTE=90

# Optional
SENTRY_DSN=
SENTRY_ENVIRONMENT=local
```

Notes:

- Groq is used for ultra-fast chat generation. For PDF embeddings, set `GOOGLE_API_KEY` (free from Google AI Studio) or `OPENROUTER_API_KEY`.
- Set `EMBEDDING_OPENAI_DIRECT=true` only if you want direct OpenAI embeddings through `OPENAI_DIRECT_API_KEY`.
- For production, set `CORS_ORIGINS` to your deployed frontend URL (e.g. on Vercel).

### Frontend

Create `frontend/.env`:

```env
VITE_API_BASE_URL=http://localhost:8000
VITE_DEV_PROXY_TARGET=http://127.0.0.1:8000
VITE_APP_ENV=local
VITE_SENTRY_DSN=
VITE_SENTRY_TRACES_RATE=0
```

For production, set:

```env
VITE_API_BASE_URL=https://your-backend-domain.com
```

## Local Development

### 1. Install frontend dependencies

```bash
cd frontend
npm install
```

### 2. Start backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Backend URLs:

```text
http://127.0.0.1:8000/health
http://127.0.0.1:8000/docs
```

### 3. Start frontend

```bash
cd frontend
npm run dev
```

Frontend URL:

```text
http://localhost:5173
```

## Quality Checks

From the repo root:

```bash
npm run build
npm run check
npm run lint
```

Frontend-only:

```bash
cd frontend
npm run typecheck
npm run lint
npm run build
```

Backend checks:

```bash
cd backend
pip install -r requirements.txt -r requirements-dev.txt
ruff check app
mypy app
python -m unittest discover -s tests -p "test_*.py"
```

## Deployment

Deploy the frontend and backend separately.

### Backend: Docker Service

Backend-specific deploy notes are in `backend/DEPLOYMENT.md`.

Use `backend/Dockerfile`.

Recommended settings for Coolify or another Docker host:

```text
Build pack: Dockerfile
Base directory: backend
Dockerfile path: /Dockerfile
Container port: 3000
Health check path: /health
```

Production backend env:

```env
PORT=3000
CORS_ORIGINS=https://your-frontend-domain.com
DEFAULT_PROVIDER=groq
DEFAULT_MODEL=llama-3.3-70b-versatile
GROQ_API_KEY=your_key
FAISS_PERSIST_DIR=faiss_index
MAX_VECTOR_SESSIONS=64
FAISS_SESSION_MAX_AGE_DAYS=3
RATE_LIMIT_UPLOAD_PER_MINUTE=8
RATE_LIMIT_ASK_PER_MINUTE=90
```

If you want FAISS indexes to survive container recreation, mount a persistent volume at:

```text
/app/faiss_index
```

### Frontend: Vercel

Use the `frontend/` directory.

```text
Root directory: frontend
Framework preset: Vite
Install command: npm install
Build command: npm run build
Output directory: dist
```

Set this Vercel environment variable:

```env
VITE_API_BASE_URL=https://your-backend-domain.com
```

After the frontend domain is known, update backend `CORS_ORIGINS` and redeploy the backend.

## Troubleshooting

- CORS error: add the exact frontend origin to `CORS_ORIGINS` and redeploy backend.
- Frontend calls localhost in production: set `VITE_API_BASE_URL` in Vercel and redeploy frontend.
- Upload works but chat says no PDF is loaded: make sure the frontend sends the same `X-Chat-Session-Id` for upload and ask requests.
- Model response fails: verify at least one provider key is valid.
- Embedding fails with cloud providers: use a valid embedding provider key or rely on local `sentence-transformers`.
- PDF data disappears after redeploy: mount persistent storage for `/app/faiss_index`.

## VS Code Workspace

Open the project workspace with:

```text
rag-pdf-chat.code-workspace
```

The workspace includes the root folder plus separate `frontend` and `backend` folders for cleaner Python and TypeScript tooling.

## License

This project is licensed under the MIT License.
