# Recall - Your Personal Digital Memory

## What we are building

Recall is a private app that remembers documents and visual information a user gives it. A user uploads PDFs, text files, Markdown files, screenshots, and pictures; later they can search them or ask a question in ordinary language. Recall answers from the user's material and shows the exact source chunks, pages, or image regions that support it.

The user does not need to keep a conversation open. They can upload material today, leave, and return days or months later with a vague memory such as "What was that deployment decision?" Recall searches the durable private library, not the history of a chat session, then returns the relevant documents, a short cited summary, and follow-up Q&A.

The goal is not simply a chatbot. It is a trustworthy personal memory system: users see what was stored, understand why an answer was given, and permanently remove material.

## Final product picture

~~~
User uploads files, notes, screenshots, and images
                    |
                    v
Recall extracts text and visual descriptions
                    |
                    v
Recall splits content into searchable chunks and indexes it
                    |
          +---------+----------+
          |                    |
          v                    v
      Search screen          Ask Recall
          |                    |
          +---------+----------+
                    v
         Results and answers with sources
                    |
                    v
      User opens, corrects, exports, or deletes sources
~~~

## Fixed technical direction

- Frontend: HTML, CSS, and vanilla JavaScript.
- Backend: hybrid architecture. Django is the main application for accounts, admin, document library, and product rules. FastAPI is the focused RAG service for ingestion jobs, embedding, retrieval, reranking, and answer generation.
- Shared storage: PostgreSQL for structured data, object storage for originals, and a vector index for embeddings. Start with PostgreSQL plus pgvector unless evidence later requires a dedicated vector store.
- Background jobs handle parsing, OCR, embedding, indexing, reprocessing, and deletion.
- AI answers: start with Groq as the single initial Q&A provider. Keep a provider adapter so OmniRoute, BYOK, or a self-hosted model can be added later without changing the RAG pipeline.

## MVP scope

The MVP supports PDF, TXT, Markdown, pasted text, screenshots, and image files; PDF extraction and OCR; page/source locations; a personal library; keyword plus semantic search; cited answers; image search through OCR/description; and deletion that removes originals and derived searchable data.

Later, not MVP: email/drive connectors, audio/video transcription, collaboration, organization accounts, proactive reminders, and agentic multi-step research.

## RAG in simple terms

Retrieval-augmented generation means Recall first searches the user's own knowledge, then provides selected evidence to the language model. The model should not answer from imagination when evidence is absent.

~~~
Question -> find likely chunks -> rank them -> make evidence pack
         -> generate cited answer -> allow source inspection
~~~

## The essential product loop

~~~
1. Upload once
2. Background processing extracts text/OCR and creates a searchable index
3. User can close the app; the original file and its index remain private in storage
4. Later, user describes what they remember in their own words
5. Recall finds evidence, summarizes it with citations, and answers follow-up questions
~~~

This requires persistent object storage, PostgreSQL records, and a vector index. It does not require conversational memory to work.

## Two kinds of stored information

Recall keeps two separate types of information:

~~~
Permanent knowledge
  original uploads + extracted text/OCR + chunks + vectors + source metadata
  used to find information months later

Conversation history
  optional question/answer threads + citations + feedback
  used only to make follow-up questions feel natural
~~~

Deleting a document removes its permanent knowledge and makes it unavailable to both new and existing conversations.

## For a new coding agent

Before making changes, read [the agent handoff guide](docs/Phase-Markdowns/AGENT_START_HERE.md), then read the current phase file under docs/Phase-Markdowns. Keep the README and phase documents updated whenever a technical decision changes.

## Risks and simple protections

| Risk | How Recall handles it |
|---|---|
| The answer makes something up | Require citations; abstain or ask for clarification when evidence is weak. |
| A user sees someone else's content | Every database, vector, object-storage, and API operation is workspace-scoped. |
| A screenshot contains sensitive data | Treat it as private content; minimize logs; offer clear deletion. |
| A parser fails | Show failed status and a useful error; never pretend it is searchable. |
| A document tries to manipulate AI | Treat uploaded content as data, never as instructions or permission. |
| Deletion is incomplete | Remove source, extracted text, chunks, embeddings, and search entries through a verified job. |

## Git and GitHub agreement

~~~
main
  +-- feature/phase-01-rag-basics
  +-- feature/phase-03-django-foundation
  +-- feature/phase-04-fastapi-rag-service
  +-- feature/phase-06-frontend-library
~~~

- Create one focused feature/fix branch at a time.
- Commit every meaningful, working change with a clear message.
- Keep secrets, uploads, local databases, and embeddings out of Git.
- Open a pull request before merging into main, even when working alone.
- Run tests and a quick manual check before merge; tag stable demo releases.

## Reading order
docs/Phase-Markdowns/
1. Phase_00_Project_Setup_and_Git.md
2. Phase_01_Learn_RAG_Fundamentals.md
3. Phase_02_Design_and_Test_RAG_Pipeline.md
4. Phase_03_Backend_Foundation_Django.md
5. Phase_04_RAG_Service_FastAPI.md
6. Phase_05_Backend_Integration_and_Evaluation.md
7. Phase_06_Frontend_Vanilla_JS.md
8. Phase_07_Hardening_Beta_and_Iteration.md

Each phase has a goal, tasks, diagram, deliverables, acceptance criteria, and Git checkpoints.
