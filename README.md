# LabPilot - AI Lab Manual Assistant

LabPilot is a full-stack AI-powered laboratory manual assistant that enables students to upload lab manuals and interact with them through an intelligent chat interface. The application uses Retrieval-Augmented Generation (RAG) to provide accurate, context-aware answers grounded in the uploaded documents.

The application features a NotebookLM-inspired workspace where each uploaded lab manual becomes an interactive AI knowledge base.

---

## Features

- Secure authentication using Supabase Auth
- Create multiple workspaces
- Upload PDF lab manuals
- Automatic PDF parsing and text extraction
- Intelligent document chunking using LangChain
- Semantic search using Hugging Face embeddings
- Supabase pgvector vector database
- AI-powered answers using Groq LLM
- Chat history stored in Supabase
- NotebookLM-inspired workspace interface
- Fast document retrieval with similarity search
- Modern responsive UI built with Next.js

---

# Tech Stack

## Frontend

- Next.js 15 (App Router)
- React 19
- JavaScript
- Tailwind CSS
- shadcn/ui
- Lucide React

## Backend

- Next.js Route Handlers
- LangChain JS
- Hugging Face Inference API
- Groq API

## Database & Storage

- Supabase PostgreSQL
- Supabase Auth
- Supabase Storage
- Supabase pgvector

---

# Project Structure

```text
.
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── signup/
│   ├── dashboard/
│   ├── chat/
│   │   └── [workspaceId]/
│   ├── api/
│   │   ├── process-pdf/
│   │   ├── chat/
│   │   └── workspaces/
│   ├── page.js
│   └── layout.js
│
├── components/
│   ├── Navbar.jsx
│   ├── WorkspaceCard.jsx
│   ├── CreateWorkspaceDialog.jsx
│   ├── ChatInput.jsx
│   ├── MessageBubble.jsx
│   ├── PDFUpload.jsx
│   ├── LoadingSpinner.jsx
│   └── Logo.jsx
│
├── lib/
│   ├── supabase/
│   ├── pdf.js
│   ├── splitter.js
│   ├── embeddings.js
│   ├── rag.js
│   └── groq.js
│
├── public/
├── middleware.js
├── package.json
└── README.md
```

---

# Architecture

```text
                    User Uploads PDF
                           │
                           ▼
                Supabase Storage
                           │
                           ▼
                 Create Workspace
                           │
                           ▼
               Extract PDF Content
                           │
                           ▼
             LangChain Text Splitter
                           │
                           ▼
         Hugging Face Embeddings API
                           │
                           ▼
          Supabase pgvector Database
───────────────────────────────────────────────────────
                     User Question
                           │
                           ▼
         Generate Question Embedding
                           │
                           ▼
      Similarity Search (Top Matching Chunks)
                           │
                           ▼
             Retrieved Document Context
                           │
                           ▼
                 Groq Large Language Model
                           │
                           ▼
                   Context-Aware Answer
                           │
                           ▼
          Store Chat History in Supabase
```

---

# Workflow

```text
User Login
      │
      ▼
Create Workspace
      │
      ▼
Upload Lab Manual
      │
      ▼
PDF Processing
      │
      ▼
Chunk Document
      │
      ▼
Generate Embeddings
      │
      ▼
Store in Vector Database
      │
      ▼
Chat with Lab Manual
      │
      ▼
Retrieve Relevant Chunks
      │
      ▼
Generate AI Response
```

---

# Database Schema

## workspaces

| Column | Type |
|----------|------|
| id | UUID |
| user_id | UUID |
| title | TEXT |
| pdf_url | TEXT |
| file_name | TEXT |
| status | TEXT |
| created_at | TIMESTAMP |

---

## document_chunks

| Column | Type |
|----------|------|
| id | UUID |
| workspace_id | UUID |
| page | INTEGER |
| chunk_index | INTEGER |
| content | TEXT |
| embedding | VECTOR(384) |

---

## chats

| Column | Type |
|----------|------|
| id | UUID |
| workspace_id | UUID |
| user_id | UUID |
| created_at | TIMESTAMP |

---

## messages

| Column | Type |
|----------|------|
| id | UUID |
| chat_id | UUID |
| role | TEXT |
| content | TEXT |
| created_at | TIMESTAMP |

---

# Environment Variables

Create a `.env.local` file.

```env
NEXT_PUBLIC_SUPABASE_URL=

NEXT_PUBLIC_SUPABASE_ANON_KEY=

SUPABASE_SERVICE_ROLE_KEY=

GROQ_API_KEY=

HUGGINGFACE_API_KEY=
```

---

# Local Development

## Clone Repository

```bash
git clone <repository-url>
cd labpilot
```

## Install Dependencies

```bash
npm install
```

## Run Development Server

```bash
npm run dev
```

Application runs on:

```text
http://localhost:3000
```

---

# RAG Pipeline

The retrieval pipeline follows these steps:

1. Upload PDF to Supabase Storage
2. Extract text from PDF
3. Split text into semantic chunks
4. Generate embeddings using Hugging Face
5. Store vectors in Supabase pgvector
6. Convert user question into an embedding
7. Perform similarity search
8. Retrieve the most relevant chunks
9. Send retrieved context to Groq
10. Generate a grounded response
11. Store conversation history

---

# Deployment

## Frontend & Backend

Deploy directly on **Vercel**.

Required environment variables:

```env
NEXT_PUBLIC_SUPABASE_URL=

NEXT_PUBLIC_SUPABASE_ANON_KEY=

SUPABASE_SERVICE_ROLE_KEY=

GROQ_API_KEY=

HUGGINGFACE_API_KEY=
```

---

# Future Improvements

- Streaming AI responses
- Multi-PDF workspaces
- Source citations
- Notebook-style notes
- Voice interaction
- Workspace sharing
- AI-generated experiment summaries
- Viva preparation mode
- Dark/Light theme
- Export conversations

---

# Acknowledgements

LabPilot is inspired by modern Retrieval-Augmented Generation (RAG) systems and document-centric AI assistants such as NotebookLM, while being specifically designed for laboratory manuals and engineering education.