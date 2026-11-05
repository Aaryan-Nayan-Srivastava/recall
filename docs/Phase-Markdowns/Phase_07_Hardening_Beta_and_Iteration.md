# Phase 07 - Hardening, Beta, and Iteration

## Goal
Make Recall dependable for a small beta group, learn from real use, and improve without damaging privacy or answer quality.

## Hardening flow
~~~
Security: permissions, secrets, file validation, audit review
Privacy: provider data handling, minimal logs, deletion verification, export plan
Reliability: retries, monitoring, failure messages, backup/restore policy
Quality: RAG regressions, citation review, latency/cost monitoring
Release: onboarding, feedback, issue triage, release notes
~~~

## Beta plan
1. Invite a small group that knows it is early-stage.
2. Give each person a guided task: upload document, upload screenshot, search, ask, inspect citation, delete.
3. Collect feedback on usefulness, missing answers, wrong citations, failures, and privacy expectations.
4. Reproduce important problems with safe test material.
5. Ship fixes through focused branches and documented releases.

## Monitor
| Signal | Why |
|---|---|
| Ingestion failures by type | Finds PDF/image/OCR problems. |
| Searches without useful result | Reveals retrieval gaps. |
| Unhelpful answers | Points to evidence, prompt, or UX issues. |
| Citation clicks | Shows whether users verify answers. |
| Delete completion time | Confirms privacy behavior. |
| Cost per file/query | Keeps project sustainable. |

## Deliverables
- Beta checklist and onboarding guide.
- Monitoring or structured logs.
- Prioritized issue backlog.
- Release checklist and tagged beta release.

## Acceptance criteria
- Beta users complete the main loop without developer help.
- Known failures are visible, not silent.
- Privacy/deletion behavior is tested end to end.
- Every release traces to pull requests and meaningful commits.

## After beta
Only then consider email/drive connectors, audio/video, sharing, organization workspaces, and proactive/agentic memory. Add one at a time with the same evaluation and privacy review.
