# Recall Architecture

## Purpose

Recall is a private, durable library for a user's documents, notes, screenshots, and images. It retrieves evidence from that stored library to power search and cited answers; it does not depend on a chat session remembering earlier messages.

## Service boundaries

~~~text
Browser (HTML, CSS, vanilla JavaScript)
                 |
                 v
Django application API
                 |
                 v
FastAPI RAG service and background workers
                 |
       +---------+---------+
       |                   |
       v                   v
PostgreSQL + pgvector   Object storage
~~~

### Frontend

The browser renders the library, upload, search, Ask Recall, and source-view screens. It calls only Django's public API. It never calls FastAPI, object storage, the vector index, or an AI provider directly, and it never contains their credentials.

### Django: product and domain layer

Django owns the product's public boundary and durable business records:

- User authentication, sessions, and workspace authorization.
- Document-library records, metadata, upload validation, and document status.
- Ingestion and deletion job records, reprocessing requests, audit events, and optional conversation records.
- Public APIs for upload, library browsing, source access, search, cited answers, feedback, and deletion.
- Authorization before it requests RAG work from FastAPI.

Django is the only service the frontend may contact. It must scope every request and returned record to the authenticated user's workspace.

### FastAPI: RAG processing layer

FastAPI is an internal service invoked only by Django after authorization. It owns:

- Parsing PDFs, text, Markdown, pasted text, screenshots, and images.
- OCR and extraction metadata, including page and source locations.
- Normalization, chunking, embedding, indexing, reprocessing, and removal of derived RAG data.
- Hybrid retrieval, reranking, evidence-context construction, and grounded answer generation.
- Provider and vector-store adapters, including the initial Groq answer-provider adapter.

FastAPI returns structured, workspace-scoped results to Django. It does not make end-user access decisions, but defensively verifies the workspace/document relationship before processing or returning data.

## Data ownership and flow

~~~text
Upload -> Django validates owner and type -> object storage original
       -> Django creates Document + IngestionJob
       -> FastAPI worker extracts, chunks, and indexes
       -> Django reports READY, PROCESSING, or FAILED

Question -> Django authorizes workspace -> FastAPI retrieves fresh evidence
         -> FastAPI creates cited response -> Django returns it to browser
~~~

PostgreSQL stores product records, source metadata, chunks, and initially vectors through pgvector. Object storage holds originals. Derived RAG data must carry document and workspace IDs so retrieval and deletion remain scoped correctly.

## Privacy and deletion rules

- A workspace is the privacy boundary for database, object-storage, vector, and API operations.
- Originals, extracted text, chunks, embeddings, and search entries are persistent knowledge. Conversation history is optional and separate.
- A deletion request makes a document unavailable to new searches, previews, and answers immediately, then removes the original and all derived data through a verified deletion job.
- Every non-abstained answer must include citations that Django can open only after rechecking permission and deletion status.
- Uploaded content is data, never executable instructions for the system or model.

## Phase-level decisions

The exact parsers, OCR engine, embedding model, task queue, object-storage provider, and service-authentication method will be selected and documented in their relevant phases. They must preserve the service boundaries and privacy rules above.
