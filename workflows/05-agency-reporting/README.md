# 05 · Automated Agency Client Reporting (Marketing)

**Domain:** Digital Marketing Agencies
**Trigger:** Schedule (Monday 8:00 AM IST) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets, Gmail, Slack

## What It Does

Every Monday morning, this workflow automatically:

1. **Pulls each client's weekly campaign metrics** (spend, impressions, clicks, conversions, CPA, ROAS per channel)
2. **Writes an AI narrative summary** — trends, insights, and a specific recommendation for next week
3. **Builds a branded HTML email report** — KPI cards with WoW deltas, channel breakdown table, AI insights callout
4. **Sends it to each client** via Gmail
5. **Logs everything** to Google Sheets (audit trail) and posts a Slack recap to the agency owner

## The ROI Pitch (for selling this)

> "Your team spends 5–8 hours every week writing client reports. This does it in 90 seconds, every Monday, automatically. Your clients get a professional branded report with AI-written insights. You get a Slack recap. Zero manual work."

**Sell at:** $500–800 setup + $200–300/mo retainer (hosting, monitoring, break-fix, 1 change/mo)

## Production Features

| Feature | Implementation |
|---------|---------------|
| **Retries** | 3× on every external node (Gmail, Sheets, Slack, Gemini) |
| **Input validation** | Email format, required fields, metric type checks |
| **Idempotency** | EventKey guard prevents double-sends (checks Report Log) |
| **Audit logging** | Every run logged to Sheets: timestamp, client, status, metrics, AI confidence |
| **Error branches** | Invalid metrics → `Invalid Reports` tab; Already-sent → `Skipped Reports` tab |
| **AI confidence gating** | Low-confidence narratives flagged for human review |
| **Sticky notes** | Full canvas documentation for any developer to understand |

## Architecture

```
Schedule/Manual → Merge → Client Config → Fetch Metrics → Validate
  ├─ Invalid → Log to Sheets (Invalid Reports)
  └─ Valid → Idempotency Check
       ├─ Already Sent → Log to Sheets (Skipped Reports)
       └─ New → Gemini AI Narrative → Parse → Build HTML → Gmail Send
            → Audit Log (Sheets) → Slack Recap
```

## Nodes (26)

- 2 Triggers (Schedule + Manual)
- 1 Merge
- 3 Code (Client Config, Metrics Fetch, Validation) — *swap for Sheets/API in prod*
- 2 IF (Valid? / Already Sent?)
- 1 Code (Idempotency Check)
- 1 Gemini AI (Narrative Writer)
- 1 Code (Parse Narrative)
- 1 Code (Build HTML Report)
- 1 Gmail (Send Report)
- 3 Code (Audit Prep, Invalid Prep, Skip Prep)
- 4 Google Sheets (Report Log, Invalid Reports, Skipped Reports, + audit)
- 1 Slack (Agency Recap)
- 6 Sticky Notes (canvas documentation)

## Setup for a Real Client

1. Create a Google Sheet with tabs: `Clients`, `Metrics`, `Report Log`, `Invalid Reports`, `Skipped Reports`
2. Replace "Client Config (Demo)" with a Google Sheets node reading `Clients`
3. Replace "Fetch Weekly Metrics (Demo)" with Google Sheets node reading `Metrics` (or Google Ads / Meta API nodes)
4. Update Gmail/Slack/Sheets credentials
5. Set the schedule to the agency's preferred day/time
6. Test with Manual Trigger → verify email arrives → activate schedule

## Tags

`marketing` `reporting` `agency` `automation` `ai` `email` `client-management`
