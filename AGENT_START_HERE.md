# Recall Agent Start Here

## Purpose

This file helps a new coding agent or collaborator understand Recall quickly. Read it before planning, coding, or changing architecture.

## One-sentence product

Recall is a private long-term digital memory: users upload documents, screenshots, images, and notes now; later they describe what they remember, receive the right source material and a cited summary, then ask follow-up questions.

## Important distinction

Recall is **not** based on remembering a previous chat. It works because uploaded material is persisted and indexed:

~~~
Original file -> object storage
Extracted text/OCR -> persistent derived storage
Chunks and metadata -> PostgreSQL
Embeddings -> vector index
Later question -> retrieve from those records -> cited answer
~~~

Conversation threads are optional and separate. They make follow-up questions convenient, but must never be the only place where a user's documents are remembered.

## Fixed decisions

| Area | Decision |
|---|---|
| Backend | Hybrid: Django for the product/domain layer; FastAPI for RAG ingestion, retrieval, and generation. |
| Frontend | HTML, CSS, vanilla JavaScript. Build it after the backend is reliable. |
| Initial answer model | Groq through a provider adapter. |
| Later model choices | OmniRoute, BYOK, or a self-hosted model may implement the same adapter. |
| MVP input types | PDF, TXT, Markdown, pasted text, screenshots, and images. |
| Retrieval | Hybrid keyword plus vector search, with citations required for answers. |
| Privacy | Every operation is scoped to a private workspace; deletion removes original and derived data. |
| Working style | One feature branch at a time, meaningful commits, pull request before main merge. |

## Where to begin

1. Read README.md for the project picture.
2. Read docs/Phase-Markdowns/Phase_00_Project_Setup_and_Git.md.
3. Identify the first unfinished phase.
4. Skip the RAG learning phase.
5. Create or use a focused feature branch; do not mix unrelated work.
6. Update the relevant phase document when a decision changes.

## Phase map in plain language

| Phase | What it means |
|---|---|
| 00 | Set up the repository and safe Git habits. |
| 01 | Learn each RAG building block with small experiments. |
| 02 | Combine the learning into a tested prototype pipeline. |
| 03 | Build Django: users, private workspaces, uploads, durable records. |
| 04 | Build FastAPI: extraction, OCR, chunks, vectors, search, cited answers. |
| 05 | Connect backend pieces and test the full flow before UI work. |
| 06 | Build the simple frontend on top of stable APIs. |
| 07 | Secure, test, beta, and improve the product. |

## Non-negotiable product behavior

- Users can upload and leave.
- Processing runs in the background and must show a clear status.
- Users can return much later and ask vague natural-language questions.
- Answers must show citations and open the underlying source.
- Weak evidence must produce clarification or abstention, not invented answers.
- Users can permanently delete their source data and its derived index records.
- Existing conversations must not return material after its source document has been deleted.

## Questions an agent should ask before changing scope

- Does this change alter the MVP input types or privacy promise?
- Is it needed for the current phase, or is it later scope?
- Does it preserve workspace isolation and cited answers?
- Is there a small test that proves the feature works?
