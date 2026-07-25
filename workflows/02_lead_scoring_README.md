# 02 — AI Lead Scoring & Routing Engine (Sales)

> **Live integrations:** Slack (OAuth2) · Gmail (OAuth2) · Google Sheets (OAuth2) · Google Gemini
> **Status:** ✅ Built, connected, and executed successfully end-to-end (real API calls)

## The Problem
Inbound leads sit in a shared inbox or web-form queue. Reps triage them hours (or days) later.
Hot leads with budget and urgency cool off; low-intent leads waste selling time.

## The Manual Way
- Read each lead form / email
- Guess intent and fit from experience
- Ask around about who should own it
- Write a first-touch email from scratch
- Update the CRM sheet — if anyone remembers
- Response time: hours to days; inconsistent scoring

## The n8n Way (this workflow)
1. **Two entry points** — Manual Trigger with a realistic sample lead (demo) and a
   **Webhook** (`POST /lead-scoring`) for production form intake.
2. **AI Lead Scorer (Gemini `gemma-4-31b-it`)** — BANT-style scoring: HOT / WARM / COLD,
   with one-sentence reasoning and key buying signals, as strict JSON.
3. **Parse Score & Draft Outreach** — lenient JSON parsing + builds a personalized
   first-touch email tuned to the score tier.
4. **Route by Score** — HOT leads take the fast lane; WARM/COLD go to nurture.
5. **HOT lane:**
   - **Slack: Alert #sales-hot-leads** *(real Slack node)* — 🔥 alert with lead, reasoning,
     signals so a rep can jump on it in minutes.
   - **Gmail: Outreach Draft** *(real Gmail node)* — the personalized outreach email is
     saved as a real Gmail draft addressed to the lead.
6. **Both lanes → Sheets: Append Leads CRM** *(real Google Sheets node)* — every lead is
   logged to **Automation Portfolio Logs → Leads** with score, reasoning, signals and a
   recommended next step.

## Verified Execution
- Execution #16 — all 10 nodes green.
- Sample lead scored **HOT** → Slack alert posted to `#sales-hot-leads` (C0BKU4DJ75F),
  Gmail outreach draft created, CRM row appended.
- Spreadsheet: `1jBF0CMiwBPn9hQ3xXOHZG81SvmObiE9zNrt7E9qWXZ4` (tab: Leads)

## Impact
- Lead response time: hours → under 2 minutes for HOT leads
- Consistent, explainable scoring (reasoning + signals logged for every lead)
- Reps only touch qualified leads; nurture happens automatically
- CRM sheet is always current — zero manual data entry

## Going to Production
- Point your website form / Typeform / Facebook Lead Ads at the webhook URL.
- Optionally auto-send (not just draft) the outreach for HOT leads after a review period.
