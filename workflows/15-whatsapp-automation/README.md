# 15 · WhatsApp Business Auto-Reply + Lead Capture (Sales)

**Domain:** Sales / Customer Engagement
**Trigger:** Webhook (POST /whatsapp-message) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets, Slack, WhatsApp Business API (optional)

## What It Does

Receives WhatsApp messages via webhook, classifies intent with AI, captures leads, and sends auto-replies:

1. **Receives message** via webhook (from, message text, timestamp, profile name)
2. **Validates** phone format and message content
3. **AI classifies** each message:
   - Intent: lead / support / spam / general
   - Suggested reply (contextual, helpful)
   - Lead data extraction (name, company, need) if intent=lead
   - Confidence score
4. **Logs ALL messages** to audit trail (Sheets) — always runs
5. **If lead:** captures data to CRM (Sheets) + alerts sales team on Slack
6. **Sends auto-reply** via WhatsApp Business API (side-branch, non-blocking)

## Why It Matters

WhatsApp is THE business channel in India, LatAm, and SEA. Every SMB needs:
- Instant auto-replies (customers expect <5 min response)
- Lead capture (don't lose prospects to slow follow-up)
- Message logging (audit trail for compliance + training)

This workflow delivers all three in one production-grade system.

## The Architecture

```
Webhook (WhatsApp message) / Manual Trigger
  → Validate Message (phone format, non-empty)
  → [Invalid?] → Log to WhatsApp Messages (rejected)
  → Gemini: Classify Message (intent, reply, lead data, confidence)
  → Parse Classification (with fallback)
  → Build Processed Item
  → Sheets: Log Message (audit trail — always runs)
  → Is Lead?
    ├─ [Yes] → Build Lead Item → Sheets: Log Lead → Slack: Lead Alert
    └─ [No] → Respond: 200 OK
  → Send WhatsApp Reply (side-branch, continueOnFail — non-blocking)
  → Respond: 200 OK
```

## Production Features

- **3× retry** on every external node (Gemini, Sheets, Slack, WhatsApp)
- **Idempotency** — EventKey prevents duplicate processing
- **Confidence gating** — low-confidence classifications flagged for review
- **Non-blocking WhatsApp send** — lead capture succeeds even if WhatsApp credential isn't configured
- **Error branches** — invalid messages logged separately
- **Audit logging** — every message logged with timestamp, intent, reply

## Test Result

Tested end-to-end via webhook (execution 82 = success):
- Received test WhatsApp message (lead inquiry from marketing agency)
- AI classified as "lead" with high confidence
- Extracted lead data (name, company, need)
- Logged to WhatsApp Messages + WhatsApp Leads sheets
- Sent Slack alert to sales team
- Attempted WhatsApp reply (continueOnFail — no credential configured in demo)
- Returned `{"ok":true,"status":"message_processed"}`

## Setup for Production

1. Create Google Sheet with tabs: `WhatsApp Messages`, `WhatsApp Leads`
2. Wire the webhook to your WhatsApp Business API (Meta Cloud API or Twilio)
3. Add WhatsApp Business API credential to "Send WhatsApp Reply" node:
   - Type: Header Auth
   - Name: `Authorization`
   - Value: `Bearer YOUR_ACCESS_TOKEN`
4. Update the WhatsApp API URL with your Phone Number ID:
   - `https://graph.facebook.com/v18.0/YOUR_PHONE_NUMBER_ID/messages`
5. Set the Slack channel for lead alerts
6. Test with a real WhatsApp message

## Tags

`whatsapp` `sales` `lead-capture` `auto-reply` `ai` `customer-engagement`
