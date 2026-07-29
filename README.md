# n8n Automation Portfolio

**Sasidhar Sunkesula** · Full-Stack Developer & Automation Engineer
📧 www.sasidharsunkesula579@gmail.com · 🔗 [LinkedIn](https://linkedin.com/in/sasidhar-sunkesula) · 💻 [GitHub](https://github.com/Sasidhar-Sunkesula) · 🌐 [Live Portfolio](https://n8n-workflow-showcase-five.vercel.app/)

---

## What This Is

Nine production-grade n8n workflows demonstrating AI-powered business automation across **Support, Sales, Knowledge, Operations, Marketing, Finance, and Competitive Intelligence** — plus a shared error-handling infrastructure workflow. Every workflow runs with **real integrations** (Gmail, Google Sheets, Slack, Notion) and **real AI** (Google Gemini / gemma-4-31b-it), not mocks.

Each workflow is built to production standards: retry logic, idempotency guards, input validation, structured audit logging, human-in-the-loop approval gates, confidence gating, and a shared error handler with dead-letter queue + multi-channel alerting.

---

## Workflows

| # | Workflow | Domain | Key Pattern | Sell At |
|---|----------|--------|-------------|---------|
| 00 | [Shared Error Handler](workflows/00-error-handler/) | Infrastructure | Error Trigger → DLQ + Slack + Gmail fallback | (included) |
| 01 | [AI Email Triage & Approval-Safe Drafts](workflows/01-email-triage/) | Support | HITL approval gate + confidence threshold + idempotency | $400–700 + $150/mo |
| 02 | [AI Lead Scoring & Approval-Safe Routing](workflows/02-lead-scoring/) | Sales | Webhook auth + dedup + schema validation + routing | $500–900 + $200/mo |
| 03 | [Document Q&A Search Bot](workflows/03-doc-qa/) | Knowledge | Grounded answering + confidence gate + source citation | $500–800 + $150/mo |
| 04 | [Meeting Notes → Notion + Slack](workflows/04-meeting-notes/) | Operations | Structured JSON extraction + dedup + multi-system sync | $300–600 + $100/mo |
| 05 | [Automated Agency Client Reporting](workflows/05-agency-reporting/) | Marketing | Scheduled metrics pull → AI narrative → branded HTML email | $500–800 + $200–300/mo |
| 06 | [Lead Capture → Enrich → AI Score → CRM + Reply](workflows/06-lead-routing/) | Sales | Webhook → enrichment → AI scoring → tiered routing + instant reply | $600–1000 + $200–350/mo |
| 07 | [Invoice Extraction + Anomaly Detection](workflows/07-invoice-extraction/) | Finance | Gmail watch → AI extraction → duplicate/anomaly detection → ledger | $700–1200 + $250–400/mo |
| 08 | [Social Media Content Repurposing](workflows/08-content-repurpose/) | Marketing | 1 article → 3 platform variants (Twitter/LinkedIn/hook) → Slack review | $400–700 + $150–250/mo |
| 09 | [Competitor Change Monitor & Alerting](workflows/09-competitor-monitor/) | Intelligence | Daily competitor page monitoring → hash diff → AI summary → Slack alert | $400–700 + $150–250/mo |

---

## Production Features (all workflows)

- ✅ **Retry On Fail** — every external node (Gemini, Gmail, Slack, Sheets, Notion) retries 3× with backoff
- ✅ **Shared Error Handler** — Error Trigger → parse → Sheets DLQ → Slack alert → Gmail fallback. Attached to all workflows.
- ✅ **Idempotency** — EventKey dedup guards prevent double-processing on retries
- ✅ **Input validation** — schema enforcement before any AI call. Malformed input → rejected + logged, never reaches Gemini.
- ✅ **Structured audit logging** — every action logged to Google Sheets (timestamp, input, output, action_taken)
- ✅ **Human-in-the-loop** — Slack "Send and Wait for Response" approval gate before any outbound action
- ✅ **Confidence gating** — AI outputs below threshold routed to human review queue instead of auto-executing
- ✅ **Anomaly detection** — statistical outlier flagging (invoice amounts, lead scores)
- ✅ **Real OAuth integrations** — Gmail, Google Sheets, Slack, Notion all connected via real OAuth2 credentials
- ✅ **Canvas documentation** — sticky notes on every workflow explaining architecture, ROI, and setup

## Tech Stack

**n8n** (self-hosted) · **Google Gemini** (gemma-4-31b-it via Google AI Studio) · **Gmail** · **Google Sheets** · **Slack** · **Notion**

---

## Repository Structure

```
n8n-workflow-showcase/
├── README.md                          ← you are here
└── workflows/
    ├── 00-error-handler/
    │   ├── workflow.json              ← importable n8n workflow
    │   └── README.md
    ├── 01-email-triage/
    │   ├── workflow.json
    │   ├── README.md
    │   └── execution.png              ← verified green run
    ├── 02-lead-scoring/
    │   ├── workflow.json
    │   ├── README.md
    │   └── execution.png
    ├── 03-doc-qa/
    │   ├── workflow.json
    │   ├── README.md
    │   └── execution.png
    ├── 04-meeting-notes/
    │   ├── workflow.json
    │   ├── README.md
    │   └── execution.png
    ├── 05-agency-reporting/
    │   ├── workflow.json
    │   └── README.md
    ├── 06-lead-routing/
    │   ├── workflow.json
    │   └── README.md
    ├── 07-invoice-extraction/
    │   ├── workflow.json
    │   └── README.md
    ├── 08-content-repurpose/
    │   ├── workflow.json
    │   └── README.md
    └── 09-competitor-monitor/
        ├── workflow.json
        └── README.md
```

## How to Use

1. **Import:** n8n → Workflows → Import from File → select any `workflow.json`
2. **Credentials:** Configure OAuth2 for Gmail, Google Sheets, Slack, Notion + Google Gemini API key
3. **Sheets:** Create "Automation Portfolio Logs" spreadsheet with tabs per workflow (see each README)
4. **Slack:** Create channels: #support-triage, #sales-hot-leads, #ops-updates, #finance, #automation-errors
5. **Error Handler:** Import `00-error-handler/workflow.json` first, then set it as the Error Workflow in each production workflow's Settings
6. **Activate**

## Deployment Notes

- Self-hosted n8n v2.31.6 (Docker recommended for production)
- Google OAuth app in Testing mode with test user — publish the GCP consent screen for external client use
- `N8N_ENCRYPTION_KEY` must be backed up — losing it invalidates all stored credentials
- Webhook endpoints should be behind HTTPS (Nginx/Caddy reverse proxy + Cloudflare SSL)

---

*Built by Sasidhar Sunkesula · Full-Stack Developer & Automation Engineer*
