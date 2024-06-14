# Preprocessing Unstructured Data for LLM Applications

Calvin Wetzel

## LLM Data Preprocessing

- **Retrieval Augmented Generation (RAG)**: A technique for grounding LLM responses on validated external information
- **Contextual Integration**: RAG apps load context into a database, then retrieve content to insert into a prompt
- **Document Content**: text content from the documents. Use for keyword or similarity search in RAG apps
- **Document Elements**: building blocks of a document. Used for various RAG tasks, such as filering and chunking.
  - Title, Narrative Text, List, Item, Table, Image, etc.
- **Element Metadata**: additional information about an element. Useful for filtering in hybrid search and for identifying the source of a response.
  - Filename, Filetype, Page Number, Section, etc.

## Normalizing Content

- `unstructured_client`
- **Normalization**: chunking document elements into sections and each document type the same way
- **Serialization**: allows results of document to be used again later on
  - Common and well understood structure
  - Standard HTTP response
  - Used in multiple programming languages e.g. python, javascript, etc.
  - Can be converted to _JSONL_
`panel.widgets.FileInput()`: check out `panel` package

## What is Metadata?

- **Document Details**: provides additional information about content extract from source documents
- **Source Identification**: filename, source URL, filetype, etc.
- **Structural Metadata**: element type hierarchy information and section information
- **Search Enhancement**: metadata provides filtering options for hybrid search
- **Vector Database**: databased optimized for performing similarity search
  - _Query_ is turned into an embedding and used to search vector database (containing processed documents) for documents/information with high similarity scores; retrieve _k_ most similar documents
    - Challenges:
      - too many matches
      - most recent information
      - loss of important information
    - Solution:
      - **Hybrid Strategy**: combines semantic search with other information retrieval techniques, such as filtering and keyword search.
        - Filtering options: metadata from documents provides filtering options for hybrid search
          - Limit search to certain sections of document based on identifying features of those sections
- `chroma database` -> open source vector database offering
- **Chunking**: LLM's have finite context window
  - same query will return different content depending on how the document is chunked
  - _Chunking by Atomic Elements_ -> combining content under the same section header/Document Elements

## Preprocessing PDFs and Images

- Document Image Analysis (DIA) - allows extraction of formatting information and text from the raw image of a document
  - 1) **Document Layout Detection (DLD)** - object detection model to draw and label bounding boxes around layout elements on a document image
    - Vision Detection - indentify and classify bounding box using computer vision model
    - Text Extraction - extract text from bounding box (OCR)
    - Direct Extraction - sometimes, text can be extracted directly from the document
  - 2) **Vision Transformers (ViT)** - take a document image as input and produces a text representation of a structured output as output e.g. JSON
    - DONUT Architecture - document understanding transformer
    - Direct Conversion - _OCR_ is _not_ required - the image input is converted directly to text
    - Structured Training - train the model to output a valid JSON string with the structured document output
  - **Advantages & Disadvantages**
    - **DLD**
      - Advantages
        - Have a fixed set of element types
        - get bounding box info
      - Disadvantages
        - requries two model calls (the object detection and OCR models)
        - less flexible
    - **ViT**
      - Advantages
        - More flexible for non-standard document types, like forms
        - More adaptableto new ontologies/element types
      - Disadvantages
        - Model is generative, potentially pron to hallucination or repetition
        - Computationally expensive

## Extracting Tables

- Document (PDF, images) table information needs to be inferred
- Techniques: Table Transformers, Vision Transformers, OCR Postprocessing
- HTML Output: extract tables in HTML format to preserve structure
- **Table Transformer** - identifies bounding boxes for table cells and converts the output to HTML
  - 1) Identify tabels using the document layout model
  - 2) Run the table through the table transformer
  - Advantages:
    - Trace cells back to the original bounding boxes
  - Disadvantages:
    - multiple expensive model calls
- **Vision Transformer** - target HTML as output
  - Advantage: allows for prompting; more flexible; one model call
  - Disadvantage: generative and prone to hallucination; no bounding boxes
- **OCR Postprocessing** - OCR the table, then build the table strucutre based on patterns in the OCR output
  - Advantages: fast; accurate for well behaved tables
  - Disadvantages: requires statistical/rules based parsing; less flexible; no link back to bounding boxes in image

## Rag Bot Steps

1) Partition
2) Filter Partitions if needed
3) Chunk Partitions
4) Create/Use embeddings
5) Load Documents and embeddings into Vector Database
6) Choose Model
7) Create Prompt Template
8) Invoke Q/A with retriever filters
