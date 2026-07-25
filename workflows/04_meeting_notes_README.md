# 04 — Meeting Notes → Notion + Slack (Operations)

> **Live integrations:** Notion (API token) · Slack (OAuth2) · Google Gemini
> **Status:** ✅ Built, connected, and executed successfully end-to-end (real API calls)

## The Problem
Meetings end and knowledge evaporates. Action items live in someone's head, decisions
get re-litigated next week, and nobody can find last month's notes.

## The Manual Way
- Someone volunteers to "take notes" (badly, while participating)
- They clean the notes up later (or never)
- Action items get pasted into chat where they scroll away
- Notes live in a random doc nobody can find
- ~30–45 minutes of post-meeting admin per meeting

## The n8n Way (this workflow)
1. **Manual Trigger → Prepare Transcript** — sample Weekly Ops Sync transcript (production:
   feed transcripts in from Zoom/Meet/Teams webhooks or a recorder like Fireflies).
2. **Gemini (`gemma-4-31b-it`)** — extracts a structured recap as strict JSON:
   2–3 sentence summary, action items (task/owner/due date), and key decisions.
3. **Parse Gemini JSON** — robust parsing with graceful fallbacks.
4. **Build Recap Content** — formats the Notion page body and the Slack recap message.
5. **Notion: Create Meeting Page** *(real Notion node)* — creates a real page titled
   "Weekly Ops Sync — <date>" with Summary / Action Items / Key Decisions sections under
   the shared workspace parent page.
6. **Slack: Post Recap #ops-updates** *(real Slack node)* — posts the recap (summary,
   action items with owners and due dates, decisions) to `#ops-updates`.
7. **Final Summary** — returns counts and the live Notion URL.

## Verified Execution
- Execution #21 — all 8 nodes green.
- Real Notion page created:
  `https://app.notion.com/p/Weekly-Ops-Sync-Sample-2026-07-24-3a83281a542b81fe8deee32638fbe5d8`
- Real Slack recap posted to `#ops-updates` (C0BKAR5GJ31).

## Impact
- Post-meeting admin: 30–45 min → 0 min
- Action items have owners and due dates the moment the meeting ends
- Searchable, permanent meeting archive in Notion
- Team-wide visibility via Slack without anyone writing a summary

## Going to Production
- Wire a transcript source (Zoom Cloud Recording webhook, Fireflies, tl;dv) into the
  Prepare Transcript node.
- Point the Notion node at a dedicated "Meeting Notes" database for filtering by team/date.
