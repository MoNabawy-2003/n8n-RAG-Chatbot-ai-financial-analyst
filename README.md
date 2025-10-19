# n8n RAG Chatbot: The AI Financial Analyst

This project demonstrates a sophisticated Retrieval-Augmented Generation (RAG) chatbot built entirely on the **n8n** automation platform.

This chatbot is designed to act as a specialized **AI Financial Analyst**. Instead of giving generic answers, it leverages a custom knowledge base (e.g., financial reports, earnings calls) to provide accurate, context-aware responses to financial queries. This is achieved by combining the power of Google's Gemini LLM with a Pinecone vector database.

-----

## ⚙️ Tech Stack

  * **Workflow Automation:** [n8n.io](https://n8n.io/)
  * **LLM & Embeddings:** Google Gemini
  * **Vector Database:** Pinecone
  * **Data Source:** Google Sheets
  * **Core Concepts:** RAG, REST APIs, Vectorization

-----

## 🏗️ How It Works: The RAG Architecture

This project is built on two distinct, automated workflows within n8n:

### 1\. The Ingestion Pipeline (Data → Knowledge)

This workflow runs on a schedule to "teach" the AI new information.

1.  **Extract:** Data (e.g., financial statements, market analysis) is read from **Google Sheets**.
2.  **Transform (Embed):** The text from each row is sent to the **Google Gemini (Embeddings)** API to be converted into vector representations.
3.  **Load:** These vectors, along with their original text metadata, are "upserted" (uploaded) into the **Pinecone** vector database, creating a searchable knowledge base.

### 2\. The Chatbot Pipeline (Query → Answer)

This workflow is triggered by a **Webhook** and provides the user's answer.

1.  **Receive Query:** A user sends a question (e.g., `"What was the main driver of revenue in Q2?"`) to the webhook.
2.  **Vectorize Query:** The user's question is sent to the **Google Gemini (Embeddings)** API to find its vector.
3.  **Retrieve Context:** This query vector is used to search **Pinecone**, which returns the most semantically similar "chunks" of text (the context) from the knowledge base.
4.  **Augment & Generate:** The original query and the retrieved context are combined into a new, complex prompt. This prompt is sent to the **Google Gemini (Chat Model)**.
5.  **Respond:** Gemini generates a final, intelligent answer based *only* on the provided context, which is then sent back to the user.

-----

## 🚀 Features

  * **Context-Aware:** The chatbot answers questions based on a specific, private dataset, eliminating generic responses or "hallucinations."
  * **Fully Automated:** The knowledge base updates automatically as new data is added to Google Sheets.
  * **Low-Code Power:** Demonstrates how complex AI applications like RAG can be built and managed visually using n8n.

-----

## Usage

The two workflows (`.json` files) can be imported directly into your n8n instance.

### Prerequisites

  * An [n8n](https://n8n.io/) instance (cloud or self-hosted).
  * A [Google AI Studio](https://ai.google.dev/) API Key.
  * A [Pinecone](https://www.pinecone.io/) API Key.

### Running the Chatbot

1.  **Run the Ingestion Pipeline** (`ingestion-workflow.json`) at least once to populate the Pinecone database.
2.  **Activate the Chatbot Pipeline** (`rag-chatbot-workflow.json`).
3.  Send a POST request to the workflow's Webhook URL:

<!-- end list -->

```bash
curl -X POST -H "Content-Type: application/json" \
-d '{"query": "What was the main driver of revenue in Q2?"}' \
[YOUR_N8N_WEBHOOK_URL]
```

### Example Response:

```json
{
  "answer": "Based on the Q2 2025 earnings report, the main driver of revenue was a 15% increase in subscription services, primarily from the new enterprise-tier platform."
}
```
