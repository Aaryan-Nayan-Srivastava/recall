# Phase 05 - Backend Integration and Evaluation

## Goal
Connect Django and FastAPI into one reliable backend experience, then prove it with tests and RAG evaluation before building the user interface.

## Integration flow
~~~
Client request -> Django authenticates/checks workspace
-> Django asks FastAPI to process/search/answer
-> FastAPI returns workspace-scoped data
-> Django records safe status/audit data
-> Django returns stable API response
~~~

## Test cases
- Upload to searchable lifecycle.
- PDF page citation and Markdown heading citation.
- Screenshot OCR search and image source opening.
- Vague natural-language search returns relevant stored evidence after the user has signed out and returned later.
- Source-preview and citation-evidence access reject documents from another workspace.
- New question and follow-up question both retrieve fresh evidence from the private library.
- Deleting a document prevents it from being found, opened, or cited by an existing conversation.
- Reprocess after parser/OCR/pipeline changes keeps the document ownership and audit history correct.
- Keyword-only, vector-only, and hybrid comparisons.
- Questions that should answer versus abstain.
- Failed parser retry and deletion during/after ingestion.
- Groq provider timeout, rate-limit, and malformed-response handling.
- Provider adapter contract tests, so a later OmniRoute or BYOK implementation can be substituted safely.
- Workspace isolation across Django, FastAPI, PostgreSQL, object storage, and vector search.

## Evaluation
| Measure | Meaning |
|---|---|
| Recall at 5 | Was right evidence in the first five chunks? |
| Citation correctness | Does each citation support the claim? |
| Abstention quality | Did Recall decline with insufficient evidence? |
| Ingestion success | Do supported files become searchable reliably? |
| Latency/cost | Is experience fast and affordable enough? |

## Deliverables
- End-to-end backend test suite.
- Gold set of at least 30 representative questions.
- Evaluation runner comparing pipeline configurations.
- Error/retry policy and frontend-facing API documentation.
- Provider-failure policy: safe user message or abstention, trace ID, and no secret or raw context in error logs.
- API contract tests covering the public Django routes and private FastAPI routes.

## Acceptance criteria
- Upload -> job complete -> search -> ask -> cited answer -> delete works without frontend.
- Evaluation catches retrieval regression.
- Baseline chunking, embedding, retrieval, reranking, and prompt are documented.
- Security review finds no known cross-workspace access path.
- Provider failures are handled predictably and the answer service can be switched through its adapter without changing retrieval behavior.
- All product flows work through Django: upload, wait, return later, search vaguely, inspect sources, ask, follow up, reprocess, give feedback, and delete.

## Git workflow
~~~
feature/django-fastapi-service-auth
feature/backend-e2e-ingestion
feature/rag-evaluation-runner
fix/deletion-vector-cleanup
~~~
