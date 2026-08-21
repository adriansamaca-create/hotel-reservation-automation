# Testing Metrics — Hotel Reservation Operations Automation Platform

## General

| Metric | Value |
|--------|-------|
| Total emails processed | 93 |
| Testing period | July 17 – August 21, 2026 |
| Model used | `models/gemini-3.1-flash-lite` |
| Languages tested | Spanish, English |

---

## Classification Results

| Category | Count | % |
|----------|-------|---|
| Information | 41 | 44.1% |
| Cancellation | 12 | 12.9% |
| Modification | 11 | 11.8% |
| Complaint | 8 | 8.6% |
| Upgrade | 8 | 8.6% |
| Invoice | 4 | 4.3% |
| **Revisión Manual** | **8** | **8.6%** |

---

## Confidence Score

| Range | Count | % |
|-------|-------|---|
| High (≥ 0.9) | 87 | 93.5% |
| Low (< 0.9) → manual review | 1 | 1.1% |
| No data (error branch) | 5 | 5.4% |

**Average confidence on successfully classified emails: 0.97**

---

## Workflow Routing

| Branch | Count | % |
|--------|-------|---|
| Normal flow (draft generated) | 85 | 91.4% |
| Manual review (error branch) | 8 | 8.6% |

---

## Data Extraction Accuracy

| Field | Extracted | % |
|-------|-----------|---|
| Reservation number | 52 / 93 | 55.9% |
| Check-in date | 51 / 93 | 54.8% |
| Check-out date | 50 / 93 | 53.8% |
| Hotel name | 31 / 93 | 33.3% |
| Client name | 68 / 93 | 73.1% |

> Note: Fields showing "No especificado" are expected when the email genuinely does not include that information (e.g. general inquiry emails with no reservation details).

---

## Language Distribution

| Language | Count | % |
|----------|-------|---|
| Spanish | 72 | 77.4% |
| English | 16 | 17.2% |
| Not detected (error branch) | 5 | 5.4% |

---

## Time Savings Estimate

| Task | Manual process | Automated |
|------|---------------|-----------|
| Read + classify email | ~2 min | ~8 sec |
| Extract reservation data | ~2 min | ~8 sec |
| Draft response | ~5 min | ~10 sec |
| **Total per email** | **~9 min** | **~26 sec** |
| **93 emails processed** | **~13.9 hours** | **~40 min** |

**Estimated time saved: ~13 hours across 93 emails (~8.4 min per email)**

---

## Key Findings

- **93.5%** of emails were classified with high confidence (≥ 0.9)
- **Human-in-the-Loop** design: drafts are saved to Gmail, never sent automatically
- **Error handling** routes ambiguous or unclassifiable emails to manual review
- **Multilingual** support validated in both Spanish and English
- Extraction accuracy is lower for hotel name (33.3%) because many test emails omitted it — expected behavior
