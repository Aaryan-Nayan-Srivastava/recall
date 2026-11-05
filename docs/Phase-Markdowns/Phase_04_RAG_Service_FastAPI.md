# Phase 04 - RAG Service with FastAPI

## Goal
Implement processing and RAG as a focused FastAPI service. It receives trusted jobs from Django and returns structured processing, search, and answer results.

## Plain-language outcome
FastAPI turns an upload into durable search material. It is not a chatbot memory. It stores searchable representations of the user's files so that a future question can retrieve evidence even when no earlier chat is open.

## Plain-language outcome
FastAPI turns an upload into durable search material. It is not a chatbot memory. It stores searchable representations of the user's files so that a future question can retrieve evidence even when no earlier chat is open.

## FastAPI responsibilities
~~~
Django creates trusted job
        |
        v
FastAPI / worker
  |- parse PDF, TXT, Markdown, pasted text
  |- OCR screenshots and images
  |- normalize and preserve locations
  |- chunk and embed
  |- index and delete vectors
  |- retrieve and rerank evidence
  +- generate structured cited answer
~~~

## Interfaces
| Operation | Input | Output |
|---|---|---|
| Ingest | workspace ID, document ID, storage reference | status, chunks, errors, extraction metadata |
| Search | workspace ID, query, filters | ranked chunks with source locations |
| Ask | workspace ID, question, filters | answer, citations, abstained flag, trace ID |
| Follow-up answer | workspace ID, new question, limited conversation context | answer, citations, abstained flag, trace ID |
| Reprocess | workspace ID, document ID, pipeline version | refreshed extraction/chunks/index status |
| Delete | workspace ID, document ID | verified removal status |

## Internal FastAPI routes

These routes are private service-to-service routes. Django calls them after it has authenticated the user and confirmed workspace ownership.

~~~
POST   /internal/ingestions              start or resume parsing/OCR/chunking/indexing
POST   /internal/search                  return workspace-scoped ranked evidence
POST   /internal/answers                 create a cited answer from fresh evidence
POST   /internal/answers/follow-up       answer a new question with limited thread context and fresh evidence
POST   /internal/documents/{id}/reprocess  re-run the selected pipeline version
DELETE /internal/documents/{id}/index    remove chunks, vectors, and derived search data
GET    /health                           service health for deployment checks
~~~

FastAPI does not decide whether a user may access a document. It accepts only trusted, authenticated requests from Django and still validates the workspace/document relationship defensively.

## AI provider approach
- Use Groq as the initial provider for generating cited answers.
- Create a small provider adapter with one method for structured answer generation, rather than calling Groq from retrieval code directly.
- Keep the provider key on the server only; never send it to Django clients or the frontend.
- OmniRoute and BYOK are later options. They should implement the same adapter contract, not create a second RAG pipeline.

~~~
Retrieval/context builder
          |
          v
Answer provider adapter
          |
          v
Groq for MVP  ->  OmniRoute or BYOK later
~~~

## Image and screenshot flow
~~~
Image/screenshot -> validation -> OCR -> optional visual description
-> dimensions/source metadata -> searchable chunks
-> index with document and workspace IDs -> citation opens original image
~~~

## Answer response shape
~~~json
{
  "answer": "A concise answer based only on provided evidence.",
  "citations": [{"document_id": "doc_123", "chunk_id": "chunk_88", "page": 4}],
  "abstained": false,
  "reason": null,
  "trace_id": "trace_abc"
}
~~~

## Citation requirement

Every citation must include enough information for Django to retrieve the user-permitted source: document ID, chunk ID, location (such as page number or image region), and a short safe label. FastAPI must re-check that cited documents are not deleted before returning an answer.

## Deliverables
- FastAPI service with authenticated service-to-service access.
- Worker jobs for parsing, OCR, chunking, embedding, indexing.
- Search and Ask service methods.
- Groq-backed answer-provider adapter with provider/model configuration stored in server environment settings.
- Vector-store adapter and deletion support.
- Retrieval trace logs without raw private content by default.
- A clear distinction between persistent knowledge records (documents/chunks/vectors) and temporary conversation/request data.
- Private internal routes for ingestion, search, new answers, follow-up answers, reprocessing, index deletion, and health checks.
- A clear distinction between persistent knowledge records (documents/chunks/vectors) and temporary conversation/request data.

## Acceptance criteria
- PDF, Markdown, text, screenshot, and image each finish or produce actionable failure.
- Results include correct document/location metadata.
- Every non-abstained answer has citation.
- A provider timeout, rate limit, or malformed response produces a safe error or abstention and never exposes an API key.
- Delete makes a document unavailable immediately, then verifies vector/object cleanup.
- A follow-up answer retrieves fresh, non-deleted evidence; it does not trust old conversation text as the source of truth.
