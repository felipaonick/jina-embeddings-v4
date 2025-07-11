## 📘 Project Description

This multimodal pipeline extracts **text** and **images** from a PDF document, generates embeddings using **jina‑embeddings‑v4**, and indexes them in **Qdrant**, enabling semantic retrieval for both text and visual queries.

---

### 🔎 PDF Extraction with `pymupdf4llm`

Initially, the PDF is loaded via **pymupdf**. The **pymupdf4llm** library is then used to:

* Convert each page to Markdown,
* Split content into text chunks, enriched with metadata (such as page number),
* Extract embedded images and save them in the `img_out` directory.

This process produces a list of dictionaries (one per chunk) that include the Markdown text and references to the extracted images.

---

### ✂️ Text Chunking and Image Association

In the main notebook **`images_and_chunking.ipynb`**, the script:

1. Divides the extracted Markdown into fixed-length chunks (\~1,500 characters with overlap),
2. Associates each chunk with its related image paths,
3. Structures the data into objects that include text, page metadata, and image file paths.

---

### 🧠 Multimodal Embeddings with `jina‑embeddings‑v4`

**jina‑embeddings‑v4** is a powerful 3.8B-parameter model that supports both text and image embeddings in a shared semantic space. It offers:

* **Text embeddings** via `encode_text`,
* **Image embeddings** via `encode_image`,
* A 2048-dimensional vector output (single-vector mode), or late interaction multi-vector mode.

This model excels in multimodal and multilingual retrieval (\[Jina AI, 2025] ([Jina AI][1])).

The notebook **`jina‑embeddings‑v4‑test.ipynb`** provides a detailed example adapted from the official Colab referenced in the Jina blog post “Universal Embeddings for Multimodal Multilingual Retrieval” ([Jina AI][1]).

---

### 🗄️ Indexing in Qdrant with Named Vectors

A Qdrant collection named `pdf_multimodal` is created with two named vectors:

* **`text_vector`** for chunk text embeddings,
* **`image_vector`** for image embeddings.

Using `qdrant-client`, each text chunk and image is represented as a **PointStruct** with:

* A unique `id`,
* The appropriate vector(s),
* Payload such as page number, text preview, or image path.

---

### 🔍 Multimodal Retrieval

The system supports:

* **Text queries** (`query_points` on `text_vector`) — e.g., finding “executive officers of Nike” returns relevant chunks with similarity > 0.82.
* **Image queries** (`query_points` on `image_vector`) — retrieving visually similar images (similarity > 0.98).
* **Combined queries** (`search`) — using both text and image embeddings in one request.

---

## 📂 Main Files

* **`jina‑embeddings‑v4‑test.ipynb`**
  A well-documented notebook example based on the official Colab, showcasing usage of the model for text and image embeddings, as described in the Jina blog on universal embeddings ([Jina AI][1]).

* **`images_and_chunking.ipynb`**
  The primary script that executes the full pipeline: PDF extraction, text chunking, image embedding, and Qdrant indexing.

---

## ✅ Summary

This project delivers a complete and reproducible multimodal retrieval pipeline featuring:

* Structured PDF parsing,
* Multimodal embeddings (text + image),
* Named-vector indexing in Qdrant,
* Accurate retrieval for both textual and visual content.

The foundation is ideal for expansion into advanced applications such as multimodal QA, dynamic payload filtering, or integration with other multilingual embedding models.

---

### 🔭 Next Steps

* Add advanced query filtering (e.g., page filters).
* Extend to QA pipelines and chat interfaces.
* Explore late-interaction retrieval strategies supported by jina‑embeddings‑v4.

---

 

* [Jina AI](https://jina.ai/news/jina-embeddings-v4-universal-embeddings-for-multimodal-multilingual-retrieval/?utm_source=chatgpt.com)

[1]: https://jina.ai/news/jina-embeddings-v4-universal-embeddings-for-multimodal-multilingual-retrieval/?utm_source=chatgpt.com "Universal Embeddings for Multimodal Multilingual Retrieval - Jina AI"
