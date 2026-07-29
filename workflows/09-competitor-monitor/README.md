# 09 · Competitor Change Monitor & Alerting (Intelligence)

**Domain:** Competitive Intelligence / Marketing / SaaS
**Trigger:** Schedule (daily 7 AM) + Manual
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** HTTP Request, Google Sheets, Slack

## What It Does

On a daily schedule, monitors competitor pages (pricing, features, homepage) and alerts the team only when something meaningful changes:

1. **Fetches** each tracked competitor's page via HTTP
2. **Extracts** text content and computes a content hash
3. **Diffs** against the last snapshot — if unchanged, skips silently (no noise)
4. **AI summarizes** what changed + business impact (only on real changes)
5. **Alerts Slack** with the change summary + recommended action
6. **Saves** the new snapshot for next comparison

## Why It Sells

Agencies and SaaS teams manually check competitor pricing and feature pages every week. It's tedious, easy to miss, and rarely done consistently. This automates it and surfaces **only meaningful changes** — no alert fatigue.

**Sell at:** $400–700 setup + $150–250/mo retainer

## The Architecture

```
Schedule (daily 7AM) / Manual Trigger
  → Competitor Targets (Sheets "Competitors" tab or demo)
  → Validate Target (URL format, page type)
  → [Invalid?] → Log to "Competitor Errors"
  → HTTP: Fetch Page
  → Extract + Hash + Diff (djb2 hash, compare to last snapshot)
  → [No change?] → Stop silently (no alert)
  → Gemini: Summarize Change (what changed + business impact)
  → Parse Summary
  → [Parse fail?] → Log to "Competitor Errors"
  → Slack: Change Alert (summary + key points + action)
  → Prepare Snapshot Row
  → Sheets: Save Snapshot (new hash for next run)
```

## Production Features

- 3× retry on every external node (HTTP, Gemini, Slack, Sheets)
- Input validation — bad URLs / page types rejected before fetching
- Hash-based idempotency — won't re-alert on identical content (no noise)
- Confidence gate — low-confidence summaries flagged "verify before acting"
- Error branches — invalid targets and parse failures route to "Competitor Errors"
- Snapshot history — every change logged with hash for audit trail

## Test Result

Tested end-to-end via manual trigger (execution 70 = success):
- Fetched competitor pages
- Computed content hashes
- AI summarized the page content + business impact
- Alerted Slack with the summary
- Saved snapshots for next comparison

## Setup for a Client

1. Create Google Sheet with tabs: `Competitors` (name, URL, pageType), `Competitor Snapshots`, `Competitor Errors`
2. Replace "Competitor Targets (Demo)" with a Sheets read of the `Competitors` tab
3. Wire the "last snapshot" lookup to the `Competitor Snapshots` tab (currently stubbed)
4. Set the Slack channel for change alerts
5. Adjust the schedule (daily/weekly) to preference

## Tags

`competitive-intelligence` `marketing` `monitoring` `ai` `automation` `alerting`
