# 🧠 AI Data Analyst Copilot

> An Agentic AI system that can understand documents, store structured and unstructured data intelligently, answer questions, perform analysis, generate predictions, and save AI-generated reports.

---

# 📖 Project Overview

Most RAG applications can only answer questions from a PDF.

Most SQL Agents can only query structured databases.

This project combines both approaches into a single intelligent system.

The application automatically determines how uploaded documents should be processed, stores the information in the appropriate database, and later routes user questions to the correct AI agent.

---

# 🎯 Goals

The project aims to solve four major problems:

- 📄 Understand uploaded documents
- 🗄 Store structured information inside MySQL
- 🧠 Store semantic knowledge inside ChromaDB
- 🤖 Use multiple AI agents to answer different types of questions

Instead of relying on one large AI model, the project follows an **Agentic AI Architecture**, where each agent has one specific responsibility.

---

# 🏗 Project Architecture

```
                           User
                             │
                             ▼
                         main.py
                             │
            ┌────────────────┴────────────────┐
            ▼                                 ▼
      Upload Document                   Ask Question
            │                                 │
            ▼                                 ▼
   Upload Supervisor                  Chat Supervisor
            │                                 │
            ▼                                 ▼
       PDF Parser                    Intent Classifier
            │                                 │
            ▼                                 ▼
   Document Classifier              SQL / RAG / ML ?
            │                                 │
     ┌──────┼───────────┐          ┌──────────┼──────────┐
     ▼      ▼           ▼          ▼          ▼          ▼
Structured Mixed  Unstructured   SQL Agent RAG Agent ML Agent
     │      │           │
     ▼      ▼           ▼
MySQL    MySQL +    ChromaDB
          ChromaDB
```

---

# 📂 Project Structure

```
AI_Data_Analyst/

│
├── agents/
│   ├── sql_agent.py
│   ├── rag_agent.py
│   └── ml_agent.py
│
├── ingestion/
│   ├── pdf_parser.py
│   ├── classifier.py
│   ├── sql_loader.py
│   ├── rag_loader.py
│   └── upload_supervisor.py
│
├── chat/
│   ├── intent_classifier.py
│   └── chat_supervisor.py
│
├── database/
│   ├── mysql.py
│   └── chroma.py
│
└── main.py
```

---

# 📌 Module Explanation

---

## main.py

This is the entry point of the application.

Every request starts here.

The user can either:

- Upload a document
- Ask a question

Depending on the action, the request is forwarded to the appropriate supervisor.

---

## Upload Supervisor

The Upload Supervisor controls the complete document ingestion pipeline.

It **does not process documents itself**.

Instead, it decides which components should process the uploaded document.

Workflow:

```
Upload PDF

↓

PDF Parser

↓

Document Classifier

↓

SQL Loader
or
RAG Loader
or
Both
```

---

## PDF Parser

The parser is responsible only for reading the uploaded document.

Responsibilities:

- Read PDF
- Extract text
- Extract tables
- Return extracted content

It does **not** perform AI reasoning.

It does **not** store data.

It simply converts the PDF into machine-readable content.

---

## Document Classifier

The Document Classifier is the first AI component in the system.

Its job is to determine the nature of the uploaded document.

Possible outputs:

```
Structured
```

```
Unstructured
```

```
Mixed
```

Example:

| Document | Classification |
|-----------|---------------|
| Employee Table | Structured |
| Company Policy | Unstructured |
| Annual Report | Mixed |
| Invoice | Structured |
| Research Paper | Unstructured |

The classifier also decides whether the document should be stored inside:

- MySQL
- ChromaDB
- Both

Example output:

```json
{
  "document_type": "mixed",
  "store_sql": true,
  "store_rag": true,
  "reason": "The document contains both tables and descriptive text."
}
```

---

## SQL Loader

The SQL Loader is responsible for storing structured information.

Example:

```
Employee Name
Salary
Department
```

↓

Converted into a DataFrame

↓

Stored inside MySQL

This module only writes data.

It never answers user questions.

---

## RAG Loader

The RAG Loader stores unstructured text.

Workflow:

```
Extract Text

↓

Chunk Text

↓

Generate Embeddings

↓

Store inside ChromaDB
```

Like the SQL Loader, this module only stores information.

It does not perform retrieval.

---

# 🤖 AI Agents

Once the document has been processed and stored, the AI agents become active.

---

## SQL Agent

The SQL Agent interacts with MySQL.

Example questions:

```
Show all employees.
```

```
List the highest paid employee.
```

```
Update salary of John.
```

Responsibilities:

- Generate SQL
- Execute SQL
- Return results

---

## RAG Agent

The RAG Agent answers questions from unstructured documents.

Example:

```
Summarize the company policy.
```

```
Explain section 5.
```

Workflow:

```
Question

↓

Similarity Search

↓

Relevant Chunks

↓

LLM

↓

Answer
```

---

## ML Agent

The ML Agent performs predictive analysis.

Future capabilities:

- Linear Regression
- Logistic Regression
- Classification
- Trend Prediction

Example:

```
Predict next month's sales.
```

---

# 💬 Chat Supervisor

The Chat Supervisor receives every user question.

Instead of answering directly, it decides which AI agent should handle the request.

Example:

Question:

```
Show all employees.
```

↓

SQL Agent

---

Question:

```
Explain the leave policy.
```

↓

RAG Agent

---

Question:

```
Predict employee attrition.
```

↓

ML Agent

The Chat Supervisor acts as the central router for all AI interactions.

---

# 🔄 Complete Upload Flow

```
User Uploads PDF
        │
        ▼
main.py
        │
        ▼
Upload Supervisor
        │
        ▼
PDF Parser
        │
        ▼
Document Classifier
        │
        ▼
Classification Result
        │
 ┌──────┴─────────┐
 ▼                ▼
SQL Loader    RAG Loader
        │
        ▼
Databases Ready
```

---

# 💬 Complete Chat Flow

```
User Question
      │
      ▼
main.py
      │
      ▼
Chat Supervisor
      │
      ▼
Intent Classifier
      │
      ▼
SQL Agent / RAG Agent / ML Agent
      │
      ▼
Answer
```

---

# 🎯 Why Separate Everything?

Each module has a **single responsibility**.

This makes the system:

- Easier to maintain
- Easier to debug
- Easier to extend
- Closer to production architecture

For example:

The SQL Agent never needs to know how PDFs are parsed.

The PDF Parser never needs to know SQL.

The RAG Agent never needs to know how MySQL works.

Each module focuses on one job and communicates through supervisors.

---

# 🚀 Future Improvements

- Multi-document RAG
- OCR support for scanned PDFs
- Excel & CSV ingestion
- Image document understanding
- Dashboard generation
- Automatic report creation
- Vector search optimisation
- Multi-agent collaboration using LangGraph
- Support for multiple LLMs
- User authentication
- Report history and versioning

---

# 🛠 Tech Stack

- Python
- LangChain
- LangGraph
- Ollama
- Qwen
- ChromaDB
- MySQL
- PyPDF
- Pandas
- Scikit-learn
- FastAPI (Future)
- Streamlit / React (Future)

---

# 🌟 Vision

The long-term goal is to build an **AI Business Intelligence Copilot** capable of:

- Understanding enterprise documents
- Performing semantic search
- Querying structured databases
- Training machine learning models
- Generating business insights
- Producing executive reports
- Assisting decision-making through multiple specialised AI agents
