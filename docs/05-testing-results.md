# Testing Results — Hotel Reservation Operations Automation Platform

## Overview

Testing was conducted between July 17 and August 21, 2026, processing **93 real emails** through the automation workflow. Emails were sent manually in batches to simulate real hotel operations, covering all defined categories in both Spanish and English.

---

## Methodology

- Emails sent manually to a dedicated Gmail account
- Workflow triggered via Gmail Trigger node in n8n
- Two separate workflows used: production (`Hotel Automatization`) and batch testing (`Testing - Batch Processing`)
- Results logged automatically to Google Sheets
- Manual review of each row to validate classification accuracy

---

## What Was Tested

### 1. Email Classification
All 6 valid categories were tested:
- Cancellation, Modification, Invoice, Upgrade, Complaint, Information

### 2. Error Handling
Ambiguous and vague emails were tested to validate the manual review branch:
- Emails with no clear category
- Corrupted/unreadable payloads
- Emails explicitly asking for help without context

### 3. Data Extraction
Each email was evaluated for correct extraction of:
- Reservation number
- Check-in / Check-out dates
- Hotel name
- Client name

### 4. Multilingual Support
Emails were sent in both Spanish (77.4%) and English (17.2%) to validate language detection and response generation.

### 5. Draft Generation
All emails routed to the normal flow received an AI-generated draft response saved to Gmail Drafts, never sent automatically (Human-in-the-Loop design).

---

## Results Summary

| Area | Result |
|------|--------|
| Total emails processed | 93 |
| High confidence classifications (≥ 0.9) | 93.5% |
| Routed to manual review | 8.6% |
| Client name extraction accuracy | 73.1% |
| Reservation number extraction | 55.9% |
| Check-in / Check-out extraction | ~54% |
| Hotel name extraction | 33.3% |
| Estimated time saved per email | ~8.4 min |

---

## Issues Found and Fixed

### Issue 1 — Rate limit on Gemini models
**Problem:** `gemini-2.0-flash` and `gemini-2.5-flash` hit free tier limits (20 RPD) during batch testing.
**Solution:** Migrated to `models/gemini-3.1-flash-lite` with 500 RPD — sufficient for all testing phases.

### Issue 2 — Email body truncation
**Problem:** Gmail node with Simplify ON only returned partial email body.
**Solution:** Set Simplify OFF in `Get Raw Payload` node to retrieve the complete email content.

### Issue 3 — JSON output as string
**Problem:** Basic LLM Chain returned the JSON as a plain text string inside `$json.text`, not as a parsed object. All field references like `$json.categoria` returned undefined.
**Solution:** Used `JSON.parse($json.text).field` pattern consistently across all nodes that consume LLM output.

### Issue 4 — Error handling logic
**Problem:** Initial If node used `$json.categoria contains "revis"` which never matched because Gemini used valid category names.
**Solution:** Rebuilt the If node with three OR conditions:
1. `Number(JSON.parse($json.text).confidence) < 0.9`
2. `JSON.parse($json.text).categoria == "Revisión Manual"`
3. `JSON.parse($json.text).categoria == "Unknown"`

### Issue 5 — Decimal separator conflict
**Problem:** Windows (Spanish locale) uses comma as decimal separator. n8n inherited this, converting `0.7` to `0,7` and breaking numeric comparisons.
**Solution:** Used JavaScript expression `{{ 0.9 }}` inside the If node instead of a plain numeric value.

---

## Key Learnings

- **Prompt engineering matters:** Adding `Unknown` as a valid category and explicit instructions for ambiguous emails significantly improved error routing accuracy.
- **Human-in-the-Loop is the right design:** Saving drafts instead of sending automatically reduces risk and allows agents to review AI-generated responses before delivery.
- **Separate testing workflow:** Having an independent batch processing workflow kept the production workflow clean and allowed controlled testing without affecting real operations.
- **LLM output is always a string:** Always parse LLM responses with `JSON.parse()` before accessing fields — never assume the output is already a structured object.

---

## Next Steps (Day 7)

- [ ] Screenshots of the complete workflow
- [ ] AS-IS vs TO-BE diagram with time estimates
- [ ] Loom video demo (3-5 min)
- [ ] Final README
- [ ] LinkedIn post
- [ ] Final GitHub commit
