# Multitenancy_Peronsal_RAG_System


> [!NOTE]  
> Source code is not public yet. If you’re reviewing this
> from my CV, feel free to contact me for a guided demo and an architecture walkthrough.

---

Note-taking and retrieval-augmented memory system for fast capture and recall.
Built for messy, real‑world note‑taking (text, images, handwritten notes) with
semantic search that finds meaning, not just keywords.

- Audience: students, knowledge workers, and anyone who captures lots of quick,
  unstructured notes.
- Core value: type or snap now; retrieve later by concept, context, and
  relationships.

---

## Overview

MemoryHub ingests text, images, and PDFs; extracts text (including handwritten
notes) via OCR; embeds content for semantic search; and retrieves relevant
snippets using a vector database. It also stores original files and structured
metadata for filtering and organization when needed.

- Stack: 
    - Laravel (API/backend)
    - Vue + Inertia
    - MariaDB (relational DB)
    - Qdrant (vector DB),
    - OpenAI embeddings
    - Google Vision OCR
    - AWS S3 object storage
- Focus: zero‑friction capture, fast ingestion, semantic retrieval.
- Multi‑tenancy: per‑user isolation across storage, metadata, and vector search.

---

## Architecture

High‑level flow:

Web (Vue/Inertia) → API (Laravel) → OCR (Google Vision) → Embeddings (OpenAI) →
Vector DB (Qdrant) → Object Storage (S3‑compatible) → Relational DB (metadata)

Components:
- Web App: Vue + Inertia front‑end for capture, search, and review.
- API/Backend: Laravel for authentication, ingestion endpoints, and search API.
- OCR: Google Vision for text extraction from images/handwriting.
- Embeddings: OpenAI for generating vector representations of content.
- Vector Search: Qdrant for semantic search with metadata filters and payloads.
- Storage: S3‑compatible object storage for originals (images/PDFs).
- Database: Relational DB (e.g., MySQL/PostgreSQL) for users, notes, and
  metadata.

Include a simple diagram (e.g., /docs/architecture.png) showing:
Capture → OCR/Text Extraction → Chunking/Embedding → Indexing (Qdrant) →
Storage (S3) → Retrieval/Re‑ranking → UI.

---

## Core Workflows

Ingestion (Text/Image/PDF):
1) Capture note (text or file upload).
2) If image/PDF, run OCR; normalize text.
3) Chunk content (by tokens/paragraphs); attach metadata (source, timestamp,
   tags).
4) Generate embeddings (can be batched); upsert into Qdrant with metadata.
5) Store original file in S3; persist metadata in the relational DB.

Retrieval:
1) User enters a natural‑language query.
2) Embed the query; run vector search in Qdrant with optional metadata filters.
3) Re‑rank and merge results (optional hybrid keyword boost).
4) Return snippets with highlighted matches and links to originals.

---

## Multi‑Tenancy

MemoryHub enforces tenant isolation at every layer:
- Vector search: each embedded chunk stores tenant identifiers (e.g., user_id
  and collection_id) in Qdrant payloads. All queries apply strict metadata
  filters to ensure users only retrieve their own vectors.
- Object storage: per‑tenant paths and signed URLs restrict access to a user’s
  files.
- Relational data: user‑scoped schemas/queries ensure isolation for notes and
  metadata.

This provides simple, reliable isolation without adding operational complexity.

---
