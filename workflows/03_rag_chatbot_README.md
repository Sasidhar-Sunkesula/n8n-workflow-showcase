# 03 — Document Q&A Search Bot (Knowledge)

> **Live integrations:** Google Gemini (grounded answers) · Google Sheets (OAuth2, Q&A audit log)
> **Status:** ✅ Built, connected, and executed successfully end-to-end (real API calls)

## The Problem
Customers (and teammates) ask the same questions over and over — pricing, refunds,
password resets, export options. Every answer costs a support ticket, and answers drift
from the source-of-truth docs.

## The Manual Way
- Customer emails or DMs a question
- Agent searches the help center / asks a colleague
- Types an answer (hopefully the current one)
- Nothing is logged, so the same question costs the same effort tomorrow

## The n8n Way (this workflow)
1. **Two entry points** — Manual Trigger (demo) and a **Webhook** (`POST /doc-qa`)
   accepting `{"question": "..."}` for embedding in a site widget or Slack command.
2. **FAQ Chunks** — the knowledge base (5 product FAQ chunks in-demo; swap for your real
   docs source — Notion export, help-center scrape, or a vector DB).
3. **Retrieve Best Chunk** — lightweight semantic retrieval (token + bigram scoring)
   selects the single most relevant document chunk for the question.
4. **Gemini (`gemma-4-31b-it`)** — answers using ONLY the retrieved context. If the answer
   isn't in the docs it says exactly *"I'm sorry, I don't have that information."* — no
   hallucinated policies.
5. **Sheets: Log Q&A** *(real Google Sheets node)* — every question/answer pair is appended
   to **Automation Portfolio Logs → DocQA Log** with source chunk + match score, giving you
   a free "what are customers actually asking?" dataset.
6. **Respond to Webhook** — returns the JSON answer to the caller.

## Verified Execution
- Execution #19 — all 8 nodes green.
- `POST /doc-qa {"question": "What is your refund policy for annual plans?"}` returned:
  *"Annual plans are eligible for a full refund within 14 days of purchase."* (source: billing)
- Row appended to spreadsheet `1jBF0CMiwBPn9hQ3xXOHZG81SvmObiE9zNrt7E9qWXZ4` (tab: DocQA Log)

## Impact
- Instant, 24/7 answers grounded in your actual docs
- Guardrailed: refuses questions outside the knowledge base
- Every interaction logged → surfaces documentation gaps and FAQ candidates
- Drop-in webhook: embed in your site, Slack, or WhatsApp via one HTTP call

## Going to Production
- Replace FAQ Chunks with your live docs source; for large corpora swap the retrieval
  node for a hosted vector store (Pinecone/Qdrant/Supabase) — the flow stays identical.
