# Phase 01 - Learn RAG Fundamentals

## Goal
Learn the pieces of RAG separately before building the product. The output is understanding and small experiments, not production code.

## What to learn
1. Document extraction: turn PDFs, Markdown, text, screenshots, and pictures into clean text plus metadata.
2. OCR: read visible text inside scans, screenshots, and images.
3. Chunking: split long content into pieces that retain meaning.
4. Embeddings: convert chunks and questions into comparable vectors.
5. Retrieval: find likely chunks for a question.
6. Reranking: improve the order of retrieved chunks.
7. Context building: fit the best evidence into the answer model's limit.
8. Grounded generation: answer with citations or decline when proof is missing.

## Learning flow
~~~
One sample document -> extract -> inspect manually -> make chunks
-> embed -> ask test question -> inspect retrieved chunks -> cited answer
~~~

## Mini-experiments
| Experiment | Compare | Record |
|---|---|---|
| Chunk size | small, medium, large | Was correct evidence retrieved? |
| Overlap | none vs modest | Did important context split? |
| Retrieval | vector-only vs keyword-only | Which questions fail? |
| Hybrid | vector plus keyword | Does it recover more evidence? |
| OCR | clean vs noisy screenshot | Is extracted text searchable? |
| Prompt | answer with/without citations | Does it stay faithful? |

## Important rule
Do not judge the system only by a beautiful answer. Inspect retrieved chunks first. If Recall retrieves wrong evidence, the answer layer cannot reliably fix it.

## Deliverables
- A notebook or small scripts.
- rag-notes.md explaining each concept in your own words.
- 15 to 25 questions with expected source passages.
- Experiment log: configuration, result, conclusion.

## Acceptance criteria
- You can explain why a chunk was retrieved.
- You can separate retrieval failure from generation failure.
- A baseline answers a few sample questions with valid source references.

## Git checkpoints
~~~
feature/rag-chunking-experiments
feature/rag-hybrid-retrieval
feature/rag-ocr-experiments
~~~
