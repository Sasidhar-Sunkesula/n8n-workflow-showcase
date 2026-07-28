# 06 · Lead Capture → Enrich → AI Score → CRM + Instant Reply (Sales)

**Domain:** B2B Sales / Lead Generation
**Trigger:** Webhook (POST /lead-intake)
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets (CRM), Gmail, Slack

## What It Does

When a lead fills out a form on your website:

1. **Validates** the submission (email format, required fields, size limits)
2. **Deduplicates** (same email + same day = skip, logged)
3. **Enriches** company data (industry, size, revenue, tech stack — via Clearbit/Apollo in prod)
4. **AI-scores** the lead 0–100 on purchase likelihood (firmographics + intent + budget signals)
5. **Routes by tier:**
   - **HOT (≥70):** Instant Slack alert to #sales-alerts with full context + "contact within 5 minutes"
   - **WARM/COLD:** Nurture path
6. **Sends a personalized instant reply** to the lead (AI-written, references their specific message)
7. **Logs everything** to Sheets CRM + returns 200 OK to the webhook caller

## The ROI Pitch

> "Speed-to-lead is everything. Leads contacted within 5 minutes are 21x more likely to close than those contacted after 30 minutes. This workflow scores, enriches, alerts your sales team, AND replies to the lead — all in under 10 seconds. Zero manual triage."

**Sell at:** $600–1,000 setup + $200–350/mo retainer

## Production Features

| Feature | Implementation |
|---------|---------------|
| **Retries** | 3× on every external node (Gemini, Gmail, Slack, Sheets) |
| **Input validation** | Email regex, name/company length, message size cap |
| **Idempotency** | EventKey = email + date; duplicates logged and skipped |
| **Enrichment fault-tolerance** | Enrichment failure is NON-FATAL — lead flows with `enriched:false` |
| **AI confidence** | Score parse fallback (defaults to WARM/40 if Gemini output is malformed) |
| **Audit logging** | Every lead logged: score, tier, reason, enrichment status, reply sent |
| **Error branches** | Invalid → `Invalid Leads`; Duplicate → `Duplicate Leads` |
| **Webhook response** | Returns JSON with lead score + tier to the caller |

## Architecture

```
Webhook POST → Validate
  ├─ Invalid → Log (Invalid Leads)
  └─ Valid → Dedup Check
       ├─ Duplicate → Log (Duplicate Leads)
       └─ New → Enrich Company → Gemini Score → Parse
            → Route by Tier
                 ├─ HOT → Slack #sales-alerts
                 └─ (all) → Gemini Draft Reply → Gmail Send
                      → CRM Log (Sheets) → 200 OK Response
```

## Setup for a Real Client

1. Create Google Sheet with tabs: `Leads`, `Invalid Leads`, `Duplicate Leads`
2. Point the client's website form to the webhook URL (n8n provides it)
3. Replace "Enrich Company (Demo)" with Clearbit/Apollo/Hunter API node
4. Update Slack channel ID for their #sales-alerts
5. Test: `curl -X POST <webhook-url> -H "Content-Type: application/json" -d '{"name":"Test User","email":"test@acmecorp.com","company":"Acme Corp","message":"Interested in your automation services"}'`

## Tags

`sales` `lead-generation` `crm` `ai-scoring` `webhook` `enrichment` `speed-to-lead`
