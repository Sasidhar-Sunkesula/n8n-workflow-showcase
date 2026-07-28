# 07 · Invoice & Document Extraction Pipeline (Finance/Ops)

**Domain:** Finance / Accounts Payable / Operations
**Trigger:** Gmail Trigger (prod) / Manual (demo)
**AI:** Google Gemini (gemma-4-31b-it)
**Integrations:** Google Sheets (Ledger), Slack

## What It Does

Watches a Gmail inbox for invoice/receipt attachments and automatically:

1. **Validates** the input (file type, size <10MB, sender domain)
2. **AI-extracts** structured data: vendor, invoice #, dates, line items, subtotal, tax, total, PO#, payment terms
3. **Detects duplicates** (same invoice # + vendor + amount already in ledger)
4. **Flags anomalies** (total > 2× the vendor's rolling average)
5. **Logs to Sheets ledger** with full audit trail + AI confidence score
6. **Alerts finance team** via Slack — normal summary or ⚠️ anomaly/duplicate warning

## The ROI Pitch

> "Your team spends 3–6 hours a week manually keying invoice data into spreadsheets. This reads every invoice email, extracts every line item, catches duplicates before you pay twice, and flags anything unusual. Zero data entry. Zero missed invoices."

**Sell at:** $700–1,200 setup + $250–400/mo retainer. **Boring = sells.** Finance teams pay for this because the cost of a duplicate payment ($757.75 in our demo) dwarfs the retainer.

## Production Features

| Feature | Implementation |
|---------|---------------|
| **Retries** | 3× on every external node (Gemini, Sheets, Slack) |
| **Input validation** | File type regex, size cap, sender email format |
| **AI extraction** | Structured JSON with per-field confidence |
| **Duplicate detection** | invoice # + vendor + amount match against ledger |
| **Anomaly detection** | Total > 2× vendor rolling average → flagged |
| **5 error branches** | Invalid input, failed extraction, duplicate, anomaly, normal |
| **Audit logging** | Every extraction logged: confidence, source, status |
| **Fault tolerance** | Extraction failure logged but doesn't crash the pipeline |

## Architecture

```
Gmail Trigger / Manual → Sample Invoices → Validate
  ├─ Invalid → Log (Invalid Invoices)
  └─ Valid → Gemini Extract → Parse
       ├─ Failed → Log (Failed Extractions)
       └─ OK → Detect Duplicates & Anomalies
            ├─ Duplicate → Log (Duplicate Invoices) → Slack ⚠️ Alert
            └─ New → Ledger Row → Sheets → Slack Summary
```

## Demo Data

Three sample invoices included:
1. **CloudHost Pro** — $757.75 hosting invoice (normal, already in ledger → duplicate)
2. **Office Supply Depot** — $3,695.60 furniture receipt (anomaly: 7.4× vendor avg)
3. **CloudHost Pro** — duplicate of #1 (caught by dedup)

## Setup for a Real Client

1. Create Google Sheet with tabs: `Invoice Ledger`, `Duplicate Invoices`, `Failed Extractions`, `Invalid Invoices`
2. Replace "Manual Trigger" + "Sample Invoices" with Gmail Trigger watching for PDF attachments
3. Add a PDF-to-text extraction step (or use Gemini Vision for image invoices)
4. Update Slack channel for #finance alerts
5. Seed the ledger with existing invoices for accurate duplicate detection

## Tags

`finance` `accounts-payable` `invoice` `document-extraction` `ai` `ocr` `anomaly-detection`
