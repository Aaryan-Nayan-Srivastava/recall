# Phase 03 - Backend Foundation with Django

## Goal
Build the product backbone first. Django owns users, workspaces, document records, permissions, admin support, and the normal product API.

## Why this phase matters
This is what makes Recall a long-term memory product instead of a temporary chat. The original upload and its metadata must remain safely stored after the user closes the app. When they return later, Django knows which private workspace owns the document and can ask the RAG service to search it.

## Why this phase matters
This is what makes Recall a long-term memory product instead of a temporary chat. The original upload and its metadata must remain safely stored after the user closes the app. When they return later, Django knows which private workspace owns the document and can ask the RAG service to search it.

## Django responsibilities
~~~
Browser / future frontend
        |
        v
Django API
  |- authentication and sessions
  |- workspace ownership checks
  |- document library records
  |- upload request and status
  |- deletion request
  |- admin and audit records
  +- calls FastAPI RAG service when needed
~~~

## Core models
| Model | Purpose |
|---|---|
| User | Identity and account information. |
| Workspace | Privacy boundary; every owned item belongs here. |
| Document | Visible item: title, type, upload path, status, checksum. |
| DocumentVersion | Extraction/parser history for reprocessing. |
| IngestionJob | Queued, working, failed, and complete processing. |
| DeletionJob | Removal of source and derived data. |
| AuditEvent | Minimal activity record without raw private content in logs. |

## Required endpoints
~~~
POST   /documents       upload document/image or pasted text
GET    /documents       list library and statuses
GET    /documents/{id}  show metadata and safe preview information
PATCH  /documents/{id}  rename a document or update tags/metadata
DELETE /documents/{id}  request deletion
POST   /documents/{id}/reprocess  process again after a failure or pipeline update
GET    /documents/{id}/content    open/download the permitted original or preview
GET    /documents/{id}/chunks/{chunk_id}  open the exact cited evidence
GET    /jobs/{id}       show ingestion/deletion progress
GET    /search?q=...    search the user's library by words and meaning
POST   /ask             ask a new grounded question and receive citations
POST   /conversations   create an optional follow-up Q&A thread
GET    /conversations/{id}  load a user's own question/answer thread
POST   /conversations/{id}/messages  ask a grounded follow-up question
POST   /feedback        report an unhelpful answer or incorrect citation
~~~

## What Django exposes and what it does not

Django is the only service the frontend calls. It verifies the user and workspace before forwarding search, answer, or processing work to FastAPI. The frontend must never call FastAPI, the vector store, object storage, or an AI provider directly.

## Upload flow
~~~
Authenticated user -> Django validates workspace and upload type
-> original file goes to object storage
-> Django creates Document + IngestionJob
-> job goes to FastAPI worker queue
-> Django exposes processing status
~~~

## Return later flow
~~~
User returns weeks later and asks a vague question
-> Django identifies the user and their workspace
-> Django sends the question only to that workspace's RAG data
-> FastAPI finds relevant stored chunks
-> Django returns cited results; no old chat history is required
~~~

## Search and follow-up flow
~~~
User says: "What was that deployment decision?"
-> GET /search finds likely stored documents/chunks
-> user opens a source page/image or asks POST /ask for a cited summary
-> Django creates or uses a conversation ID
-> POST /conversations/{id}/messages asks a follow-up
-> every answer retrieves fresh evidence from the current, non-deleted library
~~~

## Deliverables
- Django project with authentication and workspace isolation.
- PostgreSQL migrations for core models.
- Object-storage interface.
- Upload, library, status, and deletion-request APIs.
- Search, Ask Recall, source preview, citation-evidence, reprocess, and feedback APIs.
- Conversation models that store optional question/answer threads separately from documents and chunks.
- Persistent storage policy for originals, extracted text, chunks, and metadata.
- Tests proving workspace isolation.

## Acceptance criteria
- A signed-in user uploads a supported item and sees UPLOADED or PROCESSING.
- Unauthenticated requests cannot access library data.
- Changing a document ID cannot expose another workspace's document.
- Every FastAPI request has trusted workspace/document IDs.
- A user can sign out, sign in later, and still see the same private library and its finished processing status.
- A user can search, open a cited source, ask a new question, and ask a follow-up without exposing another workspace's content.
- A deleted document cannot be opened, searched, or cited by an old conversation.

## Git checkpoints
~~~
feature/django-auth-workspaces
feature/django-document-library
feature/django-upload-api
feature/django-deletion-jobs
~~~
