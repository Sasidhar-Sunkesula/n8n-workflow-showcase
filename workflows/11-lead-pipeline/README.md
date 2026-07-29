# 11 · Lead Pipeline Manager (Outreach)

**Domain:** Sales / Outreach (Meta — manages my own pipeline)
**Trigger:** Schedule (daily 9 AM) + Manual
**Integrations:** Google Sheets, Slack

## What It Does

This is a meta-workflow — it manages my own outreach pipeline. Every morning it scans the leads sheet and tells me exactly who to follow up with, with a ready-to-send message.

1. **Loads leads** from the Leads sheet (name, company, email, stage, last contact, touches, score)
2. **Computes follow-up cadence** by stage:
   - New: follow up day 3
   - Proposal sent: follow up day 3, 7, 14
   - Hot: follow up day 1, 3
   - Nurture: every 30 days
3. **Generates a follow-up message** tailored to the lead's stage
4. **Logs follow-up activity** to the Lead Pipeline sheet
5. **Alerts me on Slack** with a ready-to-send follow-up list

## Why It Matters

The fortune is in the follow-up. Most leads convert on the 3rd–5th touch, not the first. But humans are bad at consistent follow-up — leads fall through the cracks, and deals die silently.

This workflow makes sure no lead falls through the cracks. Every morning I get a Slack message: "Here are 5 leads to follow up today, with messages ready to send."

## The Architecture

```
Schedule (daily 9AM) / Manual Trigger
  → Load Leads (Sheets "Leads" tab or demo)
  → Compute Follow-up Due (cadence by stage)
  → [Not due?] → Skip
  → Build Follow-up Item (message + metadata)
  → Sheets: Log Follow-up (Lead Pipeline tab)
  → Slack: Follow-up Alert (ready-to-send message)
```

## Production Features

- 3× retry on every external node (Sheets, Slack)
- Idempotency — won't generate the same follow-up twice (email + date key)
- Stage-based cadence — different follow-up timing for new vs. proposal vs. hot leads
- Auto-nurture — leads that max out their touches move to nurture (every 30 days)
- Audit logging — every follow-up logged with timestamp, stage, touches, score

## Test Result

Tested end-to-end via manual trigger (execution 74 = success):
- Loaded 3 demo leads (Red Egg, V9 Digital, Silverback)
- Computed follow-up cadence for each
- Generated stage-appropriate follow-up messages
- Logged to Lead Pipeline sheet
- Sent Slack alert with ready-to-send messages

## Setup for Production

1. Create Google Sheet with tabs: `Leads`, `Lead Pipeline`
2. Replace "Load Leads (Demo)" with a Sheets read of the `Leads` tab
3. Wire the follow-up logging to the `Lead Pipeline` tab
4. Set the Slack channel for follow-up alerts
5. Adjust the cadence to your sales cycle

## Tags

`sales` `outreach` `pipeline` `follow-up` `automation` `crm`
