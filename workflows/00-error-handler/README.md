# 00 · Shared Error Handler & Alerting (Infrastructure)

## Problem
When any production workflow fails, the failure is silent unless someone is watching the executions tab. In a multi-workflow system, silent failures mean lost leads, unanswered emails, and stale data — often discovered days later.

## Solution Architecture
```
Error Trigger → Parse & Classify Error → Sheets: Append Error DLQ
                                        → Slack: Alert #automation-errors
                                        → Gmail: Create Internal Alert Draft (fallback)
```

![execution](execution.png)

## Production Features
- ✅ **Error Trigger** — fires automatically on ANY failure in any attached workflow
- ✅ **Error classification** — Code node extracts workflow name, failing node, error message, execution URL, and timestamp
- ✅ **Dead-letter queue** — every error appended to a Google Sheet "Error DLQ" tab for replay/audit
- ✅ **Slack alert** — posts to #automation-errors with formatted error details + execution link
- ✅ **Gmail fallback** — if Slack fails, drafts an internal alert email (never lose the notification)
- ✅ **Retry On Fail** — on all 3 output nodes (Sheets, Slack, Gmail)

## How It's Attached
Each of the 4 production workflows has `settings.errorWorkflow` set to this workflow's ID. When any node in those workflows throws an unhandled error, n8n automatically invokes this handler.

## Tech Stack
n8n · Google Sheets · Slack · Gmail

## Setup
1. Import `workflow.json` into n8n
2. Configure credentials: Google Sheets OAuth2, Slack OAuth2, Gmail OAuth2
3. Create a Google Sheet with an "Error DLQ" tab (columns: Timestamp, Workflow, Node, Error, ExecutionURL, Payload)
4. In each production workflow: Settings → Error Workflow → select this workflow
5. Activate

## Why This Matters
> "A workflow that runs green in the editor and a workflow that survives production are different artifacts." — Every senior n8n source agrees: error handling is THE #1 signal that separates a demo from a production system.
