# Phase 00 - Project Setup and Git

## Goal
Create a clean project base and a working Git/GitHub routine before product code is trusted.

## Tasks
1. Create the repository and concise README.
2. Add .gitignore for environment files, uploads, local databases, caches, embeddings, and editor files.
3. Create docs/, backend/, rag-service/, frontend/, and tests/ folders.
4. Write ARCHITECTURE.md: Django owns product/domain workflows; FastAPI owns RAG processing.
5. Create issue labels: phase, backend, rag, frontend, security, bug.
6. Add a safe development sample corpus: PDFs, Markdown, text, screenshots, and images.

## Workflow
~~~
Issue -> feature branch -> implementation + local tests
      -> meaningful commits -> pull request -> review -> merge to main
~~~

## Deliverables
- Repository pushed to GitHub.
- Folder structure and .gitignore.
- Hybrid architecture decision record.
- Project board/issues for remaining phases.
- Safe sample corpus.

## Acceptance criteria
- A fresh clone starts without a secret or upload from Git.
- Main is protected by the pull-request habit and passing checks.
- The team can explain what belongs in Django and FastAPI.

## Suggested commits
~~~
initialize Recall repository structure
~~~
