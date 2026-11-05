# Phase 02 - Design and Test the RAG Pipeline

## Goal
Turn the learning experiments into a proposed pipeline with measurable choices. This remains a controlled prototype, separate from the main product backend.

## Proposed pipeline
~~~
PDF / TXT / Markdown / pasted text / screenshot / image
                           |
                           v
          Extract text, OCR, page data, image metadata
                           |
                           v
          Normalize text and split into meaningful chunks
                           |
                           v
             Create embeddings and save vector records
                           |
                           v
Question -> keyword search + vector search -> merge candidates
                           |
                           v
                   rerank best candidates
                           |
                           v
            build evidence context with source metadata
                           |
                           v
                 answer with citations or abstention
~~~

## Design choices to test
- Preserve title, page number, section heading, image file name, and character offsets with every chunk.
- For screenshots/images, store OCR output and a short visual description so visual material is searchable with little text.
- Use hybrid retrieval: keyword search helps exact names/codes; vector search helps paraphrased questions.
- Return structured data: answer, citations, abstained, reason, trace ID.
- Restrict every record by workspace ID.

## Test corpus
Use a text-heavy PDF, scanned PDF, Markdown note, text note, clear UI screenshot, low-text image, and a document containing prompt-like instructions.

## Evaluation record
~~~
Question
Expected source/page/image
Top 5 retrieved chunks
Was expected evidence present?
Answer
Were claims supported?
Latency and estimated cost
~~~

## Deliverables
- Written pipeline specification.
- Prototype scripts and repeatable test command.
- Initial evaluation report and chunking/retrieval defaults.
- API payload shapes for FastAPI.

## Acceptance criteria
- Expected evidence appears in the first five results for most prepared questions.
- Screenshot and image content is found through OCR/metadata where appropriate.
- Unsupported questions yield a safe insufficient-evidence response.
