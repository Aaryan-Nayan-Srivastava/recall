# Phase 06 - Frontend with HTML CSS and Vanilla JavaScript

## Goal
Build a clean interface on top of the already-working backend. With stable APIs, much frontend work can be agentic-coded while you retain control of behavior and visual review.

## Screens
~~~
Sign in
  -> Library: upload, document/image list, status, filters, delete
  -> Search: query, excerpts, page/image source
  -> Ask Recall: question, cited answer, feedback
  -> Source view: document/page/image preview and highlighted evidence
~~~

## Modules
| Module | Job |
|---|---|
| api.js | Safe wrapper for backend calls and errors. |
| auth.js | Session state and sign-out. |
| library.js | Uploads, polling, filters, deletion confirmation. |
| search.js | Search form and results. |
| ask.js | Question submission, answer/citation rendering, feedback. |
| source-viewer.js | Page/image preview and evidence highlight. |
| styles/ | Tokens, layout, components, responsive/accessibility styles. |

## Key flows
~~~
Upload: choose -> basic client validation -> POST document -> poll job -> READY/FAILED/PROCESSING
Ask: enter question -> POST /ask -> loading -> answer + citations -> open source
Delete: choose -> clear confirmation -> DELETE -> hide from normal search -> poll status -> completed
~~~

## Rules
- Citations are always visible and easy to open.
- Processing and failures are clear.
- Use semantic HTML, keyboard focus, labels, contrast, and responsive layout.
- Never show verified for an abstained or uncited answer.

## Deliverables
- Responsive vanilla frontend.
- Library, upload, search, Ask Recall, source view, deletion UI.
- Accessibility and empty/error/loading state checks.

## Acceptance criteria
- A user uploads a PDF/screenshot, searches, asks, and opens cited source without help.
- Deletion cannot be confused with merely hiding an item.
- Frontend contains no database, vector-store, or model-provider credentials.

## Git checkpoints
~~~
feature/frontend-app-shell
feature/frontend-document-library
feature/frontend-search
feature/frontend-ask-recall
feature/frontend-source-viewer
~~~
