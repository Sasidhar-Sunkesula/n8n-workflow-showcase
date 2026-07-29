# 14 · Support Ticket Auto-Triage & Response

**Domain:** Customer Support / SaaS
**Trigger:** Webhook (POST /support-triage) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets, Slack

## What It Does

Watches a support inbox and auto-triages every ticket:

1. **Receives support ticket** via webhook (customer, email, subject, message, source)
2. **Validates** the ticket (email format, subject present, message > 10 chars)
3. **AI categorizes** each ticket:
   - Category: bug / feature / billing / general / spam
   - Priority: urgent / high / normal / low
   - Suggested response (draft)
   - Routing: which team should handle it (engineering / product / billing / support)
4. **Logs to Support Tickets sheet** with category, priority, routing
5. **Alerts the support team on Slack** with ticket details + suggested response

## Why It Matters

Support teams spend hours triaging tickets manually. Reading each ticket, categorizing it, deciding who should handle it, drafting a response. This automates the entire triage process in seconds. The support team gets a Slack message: "New urgent bug ticket from [customer]. Here's a draft response."

## The Architecture

```
Webhook (new ticket) / Manual Trigger
  → Support Tickets (webhook body or demo)
  → Validate Ticket (email, subject, message)
  → [Invalid?] → Log to Support Tickets (rejected)
  → Gemini: Triage Ticket (category, priority, response, routing)
  → Parse Triage (with generic fallback)
  → Build Ticket Item
  → Sheets: Log Ticket (Support Tickets tab)
  → Slack: Ticket Alert (with suggested response)
  → Respond: 200 OK
```

## Production Features

- 3× retry on every external node (Gemini, Sheets, Slack)
- Input validation — bad emails / subjects / messages rejected before AI
- Confidence gate — low-confidence triage falls back to generic response
- Error branches — invalid tickets and parse failures route to logs
- Priority-based routing — urgent tickets flagged for immediate handling
- Audit logging — every ticket logged with category, priority, routing, confidence

## Test Result

Tested end-to-end via webhook (execution 77 = success):
- Received test support ticket
- AI categorized as "bug" with "urgent" priority
- Drafted a response
- Routed to "engineering" team
- Logged to Support Tickets sheet
- Sent Slack alert with suggested response
- Returned `{"ok":true,"status":"ticket_triaged"}`

## Setup for Production

1. Create Google Sheet with tab: `Support Tickets`
2. Wire the ticket source (Zendesk, Freshdesk, email, form webhook)
3. Replace "Support Tickets (Demo)" with the real ticket source
4. Set the Slack channel for ticket alerts
5. Adjust the categorization and routing rules to your team structure

## Tags

`support` `customer-support` `triage` `ai` `automation` `saas`
