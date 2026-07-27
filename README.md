# PDF Credit Card Statement Extraction

A Python pipeline that converts transaction data trapped inside PDF credit-card
statements into a structured, analysis-ready dataset — with source-level
traceability and a reconciliation step against the statement's own printed
totals.

**Disclosure:** the statement in `data/` is a synthetic U.S. Bank-style sample.
No real account, cardholder, or transaction data is included.

## Why

Credit-card statements are laid out for people, not machines. Descriptions,
dates, and amounts that look aligned in columns are usually just space-padded
text once extracted, with no reliable column boundaries. This project inspects
that raw text first, then builds extraction rules around the patterns that
are actually there — rather than assuming a fixed format.

## Pipeline

```
PDF statement
     │
     ▼
Ingestion            pypdf, page-by-page text extraction
     │
     ▼
Statement metadata   Open/Closing date + account number (needed because
     │                transaction lines only carry MM/DD, no year)
     ▼
Normalization        whitespace cleanup
     │
     ▼
Pattern matching      regex over post date / trans date / ref / description / amount
     │
     ▼
Section-aware sign     Purchases vs. Payments & Credits
     │
     ▼
Validation             completeness + duplicate checks
     │
     ▼
Reconciliation          extracted totals vs. the statement's own printed totals
     │
     ▼
Structured DataFrame → CSV
```

## Contents

```
notebooks/
  PDFs_code_blog_v2.ipynb    walkthrough notebook: ingestion → extraction →
                              validation → reconciliation → CSV export
data/
  Sample2025 Credit Card Statements.pdf   synthetic input statement
  Sample2025_transactions_extracted.csv   extracted output (15 transactions)
```

## Result

Every purchase and payment line on the sample statement was extracted and
reconciles to a **$0.00 difference** against the statement's printed
Purchases and Payments totals — every record traceable back to its source
file, page number, and the exact matched text it came from.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook notebooks/PDFs_code_blog_v2.ipynb
```

## Stack

Python, pandas, pypdf, regular expressions, Jupyter.
