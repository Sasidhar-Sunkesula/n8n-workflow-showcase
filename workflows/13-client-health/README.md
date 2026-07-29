# 13 · Client Health Monitor (Churn Prevention)

**Domain:** SaaS / Customer Success (Churn Prevention)
**Trigger:** Schedule (daily 8 AM) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets, Slack

## What It Does

Every morning, scans client engagement data and flags at-risk clients before they churn:

1. **Loads client data** (name, company, plan, last login, feature usage, support tickets, payment status)
2. **Validates** the data (company name, plan present)
3. **AI computes a health score** (0-100) based on:
   - Login frequency (days since last login)
   - Feature usage % (are they using what they pay for?)
   - Support ticket volume (rising = frustration)
   - Payment status (late = critical risk)
4. **Flags at-risk clients** (score < 60)
5. **Alerts the success team on Slack** with health score + recommended intervention
6. **Logs to Client Health sheet** for trend tracking

## Why It Matters

SaaS companies lose most churned customers in the first 30 days. Catching at-risk clients early is 10x cheaper than acquiring new ones. This workflow gives the success team a daily heads-up: "3 clients are at risk today. Here's what to do."

## The Architecture

```
Schedule (daily 8AM) / Manual Trigger
  → Client Data (Sheets "Clients" tab or demo)
  → Validate Client (company, plan)
  → [Invalid?] → Log to Client Health (rejected)
  → Gemini: Compute Health Score (login, usage, tickets, payment)
  → Parse Health Score (with generic fallback)
  → [At Risk?] → Build Alert Item
  → Sheets: Log Health (Client Health tab)
  → Slack: At-Risk Alert (with recommended intervention)
```

## Production Features

- 3× retry on every external node (Gemini, Sheets, Slack)
- Input validation — bad company / plan rejected before AI
- Confidence gate — low-confidence scores fall back to manual review
- Error branches — invalid clients and parse failures route to logs
- Risk-based filtering — only at-risk clients trigger alerts (no noise)
- Audit logging — every health check logged with score, risk factors, intervention

## Test Result

Tested end-to-end via manual trigger (execution 76 = success):
- Loaded 3 demo clients (Brightwave, FitLife, GreenScape)
- AI computed health scores for each
- Flagged FitLife Studios as at-risk (low login, low usage, late payment)
- Logged to Client Health sheet
- Sent Slack alert with recommended intervention

## Setup for Production

1. Create Google Sheet with tab: `Client Health`
2. Wire the client data source (Sheets "Clients" tab or CRM API)
3. Replace "Client Data (Demo)" with the real data source
4. Set the Slack channel for at-risk alerts
5. Adjust the health score thresholds to your business

## Tags

`saas` `churn-prevention` `customer-success` `ai` `automation` `health-monitoring`
