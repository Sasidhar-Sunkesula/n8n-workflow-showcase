# 01 — AI Email Triage & Intent Router (Support)

> **Live integrations:** Gmail (OAuth2) · Slack (OAuth2) · Google Sheets (OAuth2) · Google Gemini
> **Status:** ✅ Built, connected, and executed successfully end-to-end (real API calls)

## The Problem
Support inboxes fill up with a mix of genuine questions, urgent complaints, upgrade requests,
FYI notifications, and spam. A human has to open every email, figure out what it is, decide
whether it needs a reply, write that reply, tell the team, and log it — before any actual
support work happens.

## The Manual Way
- Read each email (~2 min each)
- Mentally classify + prioritize it
- Write a reply from scratch (~6–10 min)
- Ping the team channel about urgent ones
- Copy details into a tracking sheet
- ~10 minutes per email, all day, every day

## The n8n Way (this workflow)
1. **Manual Trigger → Sample Inbox** — demo batch of 5 realistic support emails (swap in a
   Gmail Trigger node for production; the same Gmail credential is already connected).
2. **Gemini: Classify Intent** — `gemma-4-31b-it` classifies each email as
   Question / Request / Complaint / FYI / Spam with priority + one-line summary (strict JSON).
3. **Route by Intent** — actionable emails (Question/Request/Complaint) go down the reply lane;
   FYI/Spam go straight to the log.
4. **Gemini: Draft Reply** — writes a warm, on-brand support reply for each actionable email.
5. **Gmail: Save Reply Draft** *(real Gmail node)* — the AI reply is saved as a real draft in
   the connected Gmail account, addressed to the customer, ready for one-click human review.
6. **Slack: Alert #support-triage** *(real Slack node)* — posts classification, priority,
   summary and a "draft ready" note to the `#support-triage` channel.
7. **Sheets: Append Triage Log** *(real Google Sheets node)* — every email (actionable or not)
   is appended as a row to the **Automation Portfolio Logs → Triage Log** sheet:
   timestamp, sender, subject, classification, priority, summary, draft reply, Slack status.

## Verified Execution
- Execution #14 — all 12 nodes green.
- 5 emails in → 2 Gmail drafts created, 2 Slack alerts posted, 5 rows appended to Sheets.
- Spreadsheet: `1jBF0CMiwBPn9hQ3xXOHZG81SvmObiE9zNrt7E9qWXZ4` (tab: Triage Log)
- Slack channel: `#support-triage` (C0BKVT980BE)

## Impact
- ~10 min/email → under 1 minute of human review per actionable email
- Zero missed urgent complaints (Slack alert within seconds of triage)
- Complete audit trail in Sheets for QA and reporting
- Human stays in the loop: AI drafts, human approves and sends

## Going to Production
- Replace the sample-data node with a **Gmail Trigger** (unread in INBOX) — credential already connected.
- Optionally auto-send low-risk replies (Questions) and keep drafts only for Complaints.
