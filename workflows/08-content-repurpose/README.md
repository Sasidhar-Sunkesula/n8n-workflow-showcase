# 08 · Social Media Content Repurposing Pipeline (Marketing)

**Domain:** Content Marketing / Social Media
**Trigger:** Webhook (POST /content-repurpose) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Slack, Google Sheets

## What It Does

Takes ONE piece of long-form content (blog post, article) and turns it into THREE ready-to-post platform variants:

1. **X/Twitter thread** — hook + 3 tweets, each <280 chars, ends with CTA
2. **LinkedIn post** — professional, 150-300 words, with hashtags
3. **Short hook** — one punchy line <100 chars for ads/reels

Then routes all three to Slack for human review and logs to a content calendar.

**Nothing posts automatically.** A human reviews and posts. This is a drafting assistant, not an auto-poster — which is exactly what content teams want.

## Why It Sells

Content teams spend hours reformatting one piece of content for each platform. This turns 1 article into 3 ready-to-post pieces in ~60 seconds.

**Sell at:** $400–700 setup + $150–250/mo retainer

## The Architecture

```
Webhook / Manual Trigger
  → Source Content (webhook body or demo)
  → Validate Input (body >100 chars, ≥1 platform)
  → [Invalid?] → Log to "Content Errors"
  → Gemini: Generate Variants (twitter_thread, linkedin_post, short_hook, confidence)
  → Parse Variants (extract JSON from AI output)
  → [Parse fail?] → Log to "Content Errors"
  → Slack: Review Variants (all 3 variants for human review)
  → Prepare Calendar Row
  → Sheets: Content Calendar
  → Respond: 200 OK
```

## Production Features

- 3× retry on every external node (Gemini, Slack, Sheets)
- Input validation — short/empty content rejected before reaching AI
- Parse fallback — handles markdown-fenced and reasoning-style AI output
- Confidence gate — low-confidence variants flagged "review carefully before posting"
- Idempotency key — EventKey = title + date
- Error branches — invalid input and parse failures route to "Content Errors" tab
- Audit logging — every generation logged to "Content Calendar" with confidence score

## Test Result

Tested end-to-end via webhook with the invoice-data-entry blog post as source:
- AI generated a 3-tweet Twitter thread, a LinkedIn post, and a short hook
- All three posted to Slack for review
- Logged to Content Calendar with confidence score
- Returned `{"ok":true,"status":"variants_generated"}`

## Setup for a Client

1. Create Google Sheet with tabs: `Content Calendar`, `Content Errors`
2. Set the Slack channel for variant review
3. Send content via webhook: `POST /content-repurpose` with `{title, body, platforms}`
4. Review variants in Slack, post manually

## Tags

`marketing` `content` `social-media` `ai` `automation` `repurposing`
