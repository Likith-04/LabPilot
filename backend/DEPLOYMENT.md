# Backend Deployment Guide

This backend is a Dockerized FastAPI service. Deploy it before the frontend, then use the backend public URL as `VITE_API_BASE_URL` when deploying the frontend.

## Recommendation

Use Coolify if you already have, or are willing to run, a small VPS. It is the best fit for this backend because the app benefits from Docker, persistent storage for `faiss_index`, stable background/cache directories, and direct control over memory.

Use Railway if you want the fastest managed setup and are comfortable with usage-based billing. Railway supports Dockerfile builds and volumes.

Use Render for a simple managed deploy, but avoid the free web service for this backend. Render free web services spin down after inactivity and lose local filesystem changes; this app stores FAISS indexes on disk unless you attach persistent storage on a paid service.

## Runtime Contract

```text
Build context/base directory: backend
Dockerfile: Dockerfile
Container port: 3000
Health check path: /health
Start command: defined in Dockerfile
Persistent path, optional but recommended: /app/faiss_index
Cache path: /app/.cache
```

Run one worker per backend replica. The app keeps per-session vector stores on disk and in memory, so multiple replicas require sticky sessions or shared external storage with locking.

## Required Environment Variables

Start with `.env.example`, then set real values in the hosting platform.

Minimum production variables:

```env
PORT=3000
CORS_ORIGINS=https://your-frontend-domain.com
DEFAULT_PROVIDER=groq
DEFAULT_MODEL=llama-3.3-70b-versatile
GROQ_API_KEY=your_groq_key
FAISS_PERSIST_DIR=faiss_index
MAX_VECTOR_SESSIONS=64
FAISS_SESSION_MAX_AGE_DAYS=3
RATE_LIMIT_UPLOAD_PER_MINUTE=8
RATE_LIMIT_ASK_PER_MINUTE=90
```

For PDF indexing, configure at least one embedding-capable provider:

```env
OPENROUTER_API_KEY=your_openrouter_key
```

or:

```env
GOOGLE_API_KEY=your_google_key
```

or:

```env
HF_API_KEY=your_huggingface_key
```

Direct OpenAI embeddings are disabled by default. To use them:

```env
OPENAI_DIRECT_API_KEY=your_openai_key
EMBEDDING_OPENAI_DIRECT=true
```

If no cloud embedding provider is configured, the app falls back to local `sentence-transformers`. That works, but needs more memory and downloads model files on first use.

## Coolify

Recommended settings:

```text
New Resource: Application
Build Pack: Dockerfile
Base Directory: backend
Dockerfile Path: /Dockerfile
Port Exposes: 3000
Health Check Path: /health
```

Add environment variables from `.env.example`.

Add persistent storage if you want uploaded PDF indexes to survive container recreation:

```text
Mount path: /app/faiss_index
```

After deployment, test:

```bash
curl https://your-api-domain.com/health
curl https://your-api-domain.com/models
```

## Railway

Use the `backend` directory as the service root if possible. Railway detects a `Dockerfile` at the root of the source directory.

If the service root is the repository root instead, configure the Dockerfile path:

```env
RAILWAY_DOCKERFILE_PATH=backend/Dockerfile
```

Set the service port to `3000` if Railway does not infer it from `PORT`.

Add a volume mounted at:

```text
/app/faiss_index
```

## Render

Use a Web Service with Docker.

Recommended settings:

```text
Root directory: backend
Dockerfile path: Dockerfile
Health check path: /health
```

Set environment variables from `.env.example`.

Use a paid instance with a persistent disk mounted at:

```text
/app/faiss_index
```

Do not rely on the free web service for production-like testing of PDF uploads, because filesystem changes are ephemeral.

## Backend Smoke Test

After deploy:

```bash
curl https://your-api-domain.com/health
curl https://your-api-domain.com/models
```

Expected health response shape:

```json
{
  "status": "healthy",
  "message": "All systems operational",
  "model": "llama-3.3-70b-versatile"
}
```

After the frontend is deployed, set backend `CORS_ORIGINS` to the exact frontend origin and redeploy the backend.
