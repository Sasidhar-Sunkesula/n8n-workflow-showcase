# 10 · Customer Onboarding Automation (SaaS)

**Domain:** SaaS / Customer Success
**Trigger:** Webhook (POST /customer-onboarding) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Gmail, Google Sheets, Slack

## What It Does

When a new customer signs up, automates the entire onboarding sequence:

1. **Receives signup** via webhook (name, email, company, plan)
2. **Validates** the signup data (email format, name, plan)
3. **Idempotency check** — won't onboard the same customer twice
4. **AI writes a personalized welcome email** — references their company + plan, suggests relevant first steps, warm human tone
5. **Sends the welcome email** via Gmail
6. **Adds customer to onboarding pipeline** (Google Sheets CRM)
7. **Notifies the success team** on Slack with customer details + next steps

## Why It Sells

SaaS companies lose customers in the first 30 days due to slow, generic onboarding. New customers sit in a queue, get a templated "Welcome!" email three days later, and never hear from anyone again.

This makes onboarding **instant, personalized, and tracked.** The customer gets a warm, specific welcome within seconds of signing up. The success team gets a heads-up immediately. Nothing falls through the cracks.

**Sell at:** $600–1,000 setup + $200–350/mo retainer

## The Architecture

```
Webhook (new signup) / Manual Trigger
  → New Signup (webhook body or demo)
  → Validate Signup (email format, name, plan)
  → [Invalid?] → Log to "Onboarding Errors"
  → Idempotency Check (email + date key)
  → [Duplicate?] → Log to "Onboarding Errors"
  → Gemini: Write Welcome Email (personalized to company + plan)
  → Parse Welcome Email (with generic fallback)
  → Gmail: Send Welcome
  → Prepare CRM Row
  → Sheets: Add to Onboarding Pipeline
  → Slack: Notify Success Team
  → Respond: 200 OK
```

## Production Features

- 3× retry on every external node (Gmail, Gemini, Sheets, Slack)
- Input validation — bad emails / names rejected before processing
- Idempotency — same customer won't be onboarded twice (email + date key)
- Confidence gate — low-confidence AI emails fall back to a generic template
- Error branches — invalid signups and duplicates route to "Onboarding Errors"
- Audit logging — every onboarding logged with timestamp, customer, plan, stage

## Test Result

Tested end-to-end via webhook (execution 72 = success):
- Received test signup
- AI wrote a personalized welcome email
- Sent via Gmail
- Added to onboarding pipeline
- Notified success team on Slack
- Returned `{"ok":true,"status":"onboarded","stage":"welcome_sent"}`

## Setup for a Client

1. Create Google Sheet with tabs: `Onboarding Pipeline`, `Onboarding Errors`
2. Wire the signup webhook to your signup flow (Stripe, custom form, etc.)
3. Replace "New Signup (Demo)" with the webhook body
4. Wire the idempotency check to the `Onboarding Pipeline` tab
5. Set the Slack channel for success team notifications
6. Customize the welcome email tone/branding

## Tags

`saas` `onboarding` `customer-success` `ai` `automation` `crm`
