# 12 · Proposal Generator (Sales Automation)

**Domain:** Sales / Outreach (Meta — automates my own sales process)
**Trigger:** Webhook (POST /proposal-generator) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Gmail, Google Sheets, Slack

## What It Does

Takes discovery call notes and generates a customized, professional proposal in 60 seconds:

1. **Receives discovery notes** via webhook (company, process, hours, rate, pain points, volume)
2. **Validates** the notes (email format, company name, hours > 0)
3. **AI generates a full proposal** — problem statement, solution, ROI calculation, pricing, timeline, next steps
4. **Sends the proposal** to the prospect via Gmail
5. **Logs to pipeline** (stage: proposal_sent) with ROI metrics
6. **Notifies me on Slack** with proposal details + follow-up reminder

## Why It Matters

Proposals take 1–2 hours to write. This generates a professional, customized proposal in 60 seconds. The fortune is in the follow-up, and fast proposals win deals. Sending a proposal within minutes of a discovery call — not days — dramatically increases close rates.

## The Architecture

```
Webhook (discovery notes) / Manual Trigger
  → Discovery Notes (webhook body or demo)
  → Validate Notes (email, company, hours)
  → [Invalid?] → Log to Pipeline (rejected)
  → Gemini: Generate Proposal (problem, solution, ROI, pricing, timeline)
  → Parse Proposal (with generic fallback)
  → Gmail: Send Proposal
  → Prepare Pipeline Row (with ROI metrics)
  → Sheets: Log to Pipeline (stage: proposal_sent)
  → Slack: Proposal Sent (with follow-up reminder)
  → Respond: 200 OK
```

## Production Features

- 3× retry on every external node (Gmail, Gemini, Sheets, Slack)
- Input validation — bad emails / companies / hours rejected before AI
- Confidence gate — low-confidence proposals fall back to a generic template
- Error branches — invalid notes and parse failures route to pipeline logs
- ROI calculation — hours saved, labor cost saved, payback period computed automatically
- Audit logging — every proposal logged with ROI metrics and stage

## Test Result

Tested end-to-end via webhook (execution 75 = success):
- Received test discovery notes
- AI generated a full proposal with ROI calculation
- Sent proposal via Gmail
- Logged to pipeline with ROI metrics
- Sent Slack notification with follow-up reminder
- Returned `{"ok":true,"status":"proposal_sent"}`

## Setup for Production

1. Create Google Sheet with tab: `Lead Pipeline`
2. Wire the discovery notes webhook to your CRM / calendar / form
3. Replace "Discovery Notes (Demo)" with the webhook body
4. Set the Slack channel for proposal notifications
5. Customize the proposal template tone/branding

## Tags

`sales` `proposals` `automation` `ai` `crm` `outreach`
