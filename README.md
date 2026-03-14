# 🤖 HR Policies RAG Chatbot

A **Retrieval-Augmented Generation (RAG)** chatbot that answers employee questions about HR policies — in both **English and Arabic** — by retrieving relevant content directly from official HR policy PDF documents.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [System Architecture](#system-architecture)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Supported Policy Topics](#supported-policy-topics)
- [Pipeline Details](#pipeline-details)

---

## Overview

This project builds an interactive HR chatbot powered by RAG. It processes HR policy documents (PDF), stores them as semantic embeddings in a vector database, and uses a large language model (LLM) to generate concise, grounded answers to employee queries — without hallucination, since every answer is tied to retrieved policy context.

---

## Problem Statement

Employees frequently ask HR staff repetitive questions about policies such as leave rules, working hours, or termination procedures. Since policies are buried inside lengthy PDF documents, finding the right section takes time, leading to delays, inefficiency, and frustration.

This chatbot provides **instant, accurate answers** grounded in official HR policy documents, reducing both waiting time and repeated queries.

---

## Features

- 📄 **PDF ingestion** — Parses HR policy PDFs using `pymupdf4llm` into structured markdown
- 🔍 **Smart semantic search** — Combines vector similarity with topic-matching for more accurate retrieval
- 🌍 **Bilingual support** — Handles queries in both English and Arabic, with Arabic text normalization
- 🧠 **LLM-powered answers** — Uses `Qwen2.5-3B-Instruct` (local) with LangChain orchestration
- 🏷️ **Topic-aware retrieval** — Pre-defined topic embeddings improve chunk relevance
- 🛡️ **Vague query detection** — Prompts users to refine unclear questions
- 🐛 **Debug mode** — Inspect retrieved chunks and scores at query time

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3 |
| Environment | Google Colab |
| RAG Framework | LangChain |
| LLM | Qwen2.5-3B-Instruct (Hugging Face Transformers) |
| Embeddings | `paraphrase-multilingual-MiniLM-L12-v2` |
| Vector Store | ChromaDB |
| PDF Parsing | `pymupdf4llm` |
| Similarity | `scikit-learn` cosine similarity |
| Version Control | Git + GitHub |

---

## Project Structure

```
HRPolicesRAGpipeline/
│
├── RAGProjectHRPolicies.ipynb   # Main Colab notebook
├── HR_policies.pdf              # HR policy source document
└── README.md                    # This file
```

---

## System Architecture

```
User Query
    │
    ▼
Convert to Embeddings
    │
    ▼
Vector Database Search  ◄──── ChromaDB (pre-indexed HR policy chunks)
    │
    ▼
Retrieve Top-K Chunks  +  Topic Matching
    │
    ▼
Prompt Assembly & LLM Inference  (Qwen2.5-3B-Instruct)
    │
    ▼
Final Answer Generation  →  User
```

**Key design choice — Smart Search:** Beyond standard vector similarity, the pipeline matches each query against a curated dictionary of topic-representative questions. Retrieved chunks are filtered by semantic alignment with the most relevant topic, improving precision significantly.

---

## Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/ahmedellaboudy/HRPolicesRAGpipeline.git
```

### 2. Open in Google Colab

Open `RAGProjectHRPolicies.ipynb` directly in [Google Colab](https://colab.research.google.com/).

> **Recommended:** Use a GPU runtime (Runtime → Change runtime type → T4 GPU) for faster model inference.

### 3. Install dependencies

Run the first two cells to install all required packages:

```bash
pip install langchain langchain-community pymupdf4llm chromadb sentence-transformers
pip install transformers torch accelerate
```

### 4. Upload the HR policy PDF

When prompted by the upload cell, upload your `HR_policies.pdf` file (also available in the GitHub repo).

---

## Usage

Run all cells in order, then interact with the chatbot in the final cell:

```
=== HR Assistant Chat (English/Arabic) ===
Type 'exit', 'quit', or 'خروج' to end the conversation.
Type 'debug' followed by your query to see retrieval details.

You: What is the leave policy?
Bot: After completing the probation period, employees can take 1 CL per month...

You: ما هي ساعات العمل؟
Bot: يفتح المكتب من الساعة 10:00 صباحاً حتى 7:00 مساءً للذكور...
```

**Special commands:**

| Command | Description |
|---|---|
| `exit` / `quit` / `خروج` | End the chat session |
| `debug <your question>` | Show retrieved chunks, scores, and topic match details |

---

## Supported Policy Topics

The chatbot covers the following HR policy areas:

| English Topics | Arabic Topics |
|---|---|
| Dress Code | تقييم الأداء (Performance Evaluation) |
| Working Office Hours | التدريب والتطوير (Training & Development) |
| Leaves & Holidays | مدونة السلوك (Code of Conduct) |
| Probation Period | الصحة والسلامة (Health & Safety) |
| Employees Termination | العمل عن بعد (Remote Work) |
| Staff Payroll | تسوية الشكاوى (Grievance Resolution) |
| HR Initiatives & Engagement | الانفصال (Exit / Offboarding) |
| Mobile Phone Policy | |
| Absence & Attendance | |
| Staff Movement & Transfers | |
| Staff Orientation | |
| Interim Positions & Promotions | |
| Personnel Files | |
| Job Descriptions | |
| Suggestions | |
| Tax Deduction | |
| Late Coming | |
| Office Rooms | |
| Travelling Allowance | |

---

## Pipeline Details

### Cell-by-Cell Breakdown

| Cell | Purpose |
|---|---|
| Cell 1 | Import all dependencies |
| Cell 2 | Extract PDF content to markdown via `pymupdf4llm` |
| Cell 3 | Split markdown by headers (`#`, `##`) |
| Cell 4 | Create 2000-character overlapping chunks |
| Cell 5 | Clean text content and metadata |
| Cell 6 | Arabic text normalization (handles ا/أ/إ variants, etc.) |
| Cell 7 | Define topic-question dictionary (English + Arabic) |
| Cell 8 | Build ChromaDB vector store + smart search logic |
| Cell 9 | Load Qwen2.5-3B-Instruct model + LangChain prompt chains |
| Cell 10 | Helper functions (language detection, response cleaning) |
| Cell 11 | Main chat loop |
| Cell 12 | Entry point (`__main__`) |

### Chunking Strategy

- **Header splitting** → preserves document structure and topic metadata
- **Recursive character splitting** → 2000 chars with 200-char overlap
- **Metadata enrichment** → each chunk carries `Main Topic`, `Sub-topic`, and `chunk_id`

### Arabic Support

- Normalizes Arabic letter variants (e.g., `أ/إ/آ` → `ا`, `ة` → `ه`)
- Separate Arabic prompt template for the LLM
- Response cleaner strips non-Arabic characters from Arabic outputs

---

## Author

**Ahmed Ellaboudy**  
GitHub: [@ahmedellaboudy](https://github.com/ahmedellaboudy)
