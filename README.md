# n8n Automation Portfolio

**Sasidhar Sunkesula** · Full-Stack Developer & Automation Engineer
📧 www.sasidharsunkesula579@gmail.com · 🔗 [LinkedIn](https://linkedin.com/in/sasidhar-sunkesula) · 💻 [GitHub](https://github.com/Sasidhar-Sunkesula)

---

## What This Is

Four production-grade n8n workflows demonstrating AI-powered business automation across **Support, Sales, Knowledge, and Operations** — plus a shared error-handling infrastructure workflow. Every workflow runs with **real integrations** (Gmail, Google Sheets, Slack, Notion) and **real AI** (Google Gemini / gemma-4-31b-it), not mocks.

Each workflow is built to production standards: retry logic, idempotency guards, input validation, structured audit logging, human-in-the-loop approval gates, and a shared error handler with dead-letter queue + multi-channel alerting.

---

## Workflows

| # | Workflow | Function | Key Pattern |
|---|----------|----------|-------------|
| 00 | [Shared Error Handler](workflows/00-error-handler/) | Infrastructure | Error Trigger → DLQ + Slack + Gmail fallback |
| 01 | [AI Email Triage & Approval-Safe Drafts](workflows/01-email-triage/) | Support | HITL approval gate + confidence threshold + idempotency |
| 02 | [AI Lead Scoring & Approval-Safe Routing](workflows/02-lead-scoring/) | Sales | Webhook auth + dedup + schema validation + routing |
| 03 | [Document Q&A Search Bot](workflows/03-doc-qa/) | Knowledge | Grounded answering + confidence gate + source citation |
| 04 | [Meeting Notes → Notion + Slack](workflows/04-meeting-notes/) | Operations | Structured JSON extraction + dedup + multi-system sync |

---

## Production Features (all workflows)

- ✅ **Retry On Fail** — every external node (Gemini, Gmail, Slack, Sheets, Notion) retries 3× with backoff
- ✅ **Shared Error Handler** — Error Trigger → parse → Sheets DLQ → Slack alert → Gmail fallback. Attached to all 4 workflows.
- ✅ **Idempotency** — message_id / lead_id / meeting_id dedup guards prevent double-processing on retries
- ✅ **Input validation** — schema enforcement before any AI call. Malformed input → rejected + logged, never reaches Gemini.
- ✅ **Structured audit logging** — every action logged to Google Sheets (timestamp, input, output, action_taken)
- ✅ **Human-in-the-loop** — Slack "Send and Wait for Response" approval gate before any outbound action (email drafts, lead routing)
- ✅ **Confidence gating** — AI outputs below threshold routed to human review queue instead of auto-executing
- ✅ **Real OAuth integrations** — Gmail, Google Sheets, Slack, Notion all connected via real OAuth2 credentials

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
    └── 04-meeting-notes/
        ├── workflow.json
        ├── README.md
        └── execution.png
```

## How to Use

1. **Import:** n8n → Workflows → Import from File → select any `workflow.json`
2. **Credentials:** Configure OAuth2 for Gmail, Google Sheets, Slack, Notion + Google Gemini API key
3. **Sheets:** Create "Automation Portfolio Logs" spreadsheet with tabs per workflow (see each README)
4. **Slack:** Create channels: #support-triage, #sales-hot-leads, #ops-updates, #automation-errors
5. **Error Handler:** Import `00-error-handler/workflow.json` first, then set it as the Error Workflow in each production workflow's Settings
6. **Activate**

## Deployment Notes

- Self-hosted n8n v2.31.6 (Docker recommended for production)
- Google OAuth app in Testing mode with test user — publish the GCP consent screen for external client use
- `N8N_ENCRYPTION_KEY` must be backed up — losing it invalidates all stored credentials
- Webhook endpoints should be behind HTTPS (Nginx/Caddy reverse proxy + Cloudflare SSL)

---

*Built by Sasidhar Sunkesula · Full-Stack Developer & Automation Engineer*
