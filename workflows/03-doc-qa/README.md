# 03 · Document Q&A Search Bot (Knowledge)

## Problem
Customers repeatedly ask the same questions (hours, password reset, pricing, refunds). Support agents spend 30%+ of their time answering FAQs from memory or copy-pasting from a wiki. A searchable knowledge base that returns grounded, cited answers reduces support load and response time.

## Solution Architecture
```
Webhook (POST /doc-qa) / Manual Trigger
  → Validate Question (non-empty, > 3 words)
  → FAQ Chunks (5 knowledge-base documents)
  → Retrieve Best Chunk (token-overlap + bigram-boost scoring)
  → Retrieval Confidence Gate (IF matchScore < threshold → No-Answer Response)
  → Gemini (gemma-4-31b-it): grounded answer using ONLY retrieved context
  → Format Answer (question, answer, source, matchScore)
  → Sheets: Log Q&A (usage analytics)
  → Respond to Webhook
```

![execution](execution.png)

## Production Features
- ✅ **Grounded answering** — Gemini is instructed to answer using ONLY the retrieved document context. If the answer isn't in the context, it replies "I'm sorry, I don't have that information" instead of hallucinating.
- ✅ **Retrieval confidence gate** — IF no chunk scores above threshold → returns a polite "I don't have that information" response. Prevents hallucination on out-of-scope questions.
- ✅ **Source citation** — every answer includes the source category (hours/account/pricing/billing/data) and match score
- ✅ **Input validation** — question must be non-empty and > 3 words. Garbage input rejected early.
- ✅ **Query logging** — every question + answer + source + score → appended to "DocQA Log" sheet. Enables: usage analytics, quality review, identifying missing docs.
- ✅ **Retry On Fail** — on Gemini and Sheets nodes
- ✅ **Error handling** — attached to shared Error Handler workflow
- ✅ **Real integrations** — Google Sheets OAuth2 (Q&A log), Google Gemini API (grounded answers)

## Tech Stack
n8n · Google Gemini (gemma-4-31b-it) · Google Sheets

## Setup
1. Import `workflow.json`
2. Configure credentials: Google Sheets OAuth2, Google Gemini API
3. Create Google Sheet "Automation Portfolio Logs" with tab: DocQA Log
4. Replace FAQ Chunks Code node with your actual knowledge base documents
5. Activate

## Results / Metrics
- Sample question "How do I reset my password?" → correct grounded answer citing "account" category (verified execution #19)
- Out-of-scope question → "I'm sorry, I don't have that information" (confidence gate working)
- Average response time: ~3s (Gemini call + Sheets append)

## Upgrade Path
For large corpora (100+ documents), replace the Code-node retrieval with a hosted vector database (Qdrant/Pinecone free tier) + Gemini embeddings for semantic search. The current token-overlap approach is production-ready for < 50 documents.
