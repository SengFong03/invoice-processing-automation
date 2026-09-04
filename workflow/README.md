# Invoice Processing Automation

An n8n-based workflow that automates invoice processing from PDF files to a structured Google Sheets tracker.

## Overview

This workflow was built to reduce manual invoice data entry for an internal finance process.

When a new invoice PDF is added to a designated Google Drive folder, the workflow automatically:

1. Detects the new invoice
2. Downloads and extracts text from the PDF
3. Uses an LLM to extract structured invoice information
4. Determines the appropriate monthly spreadsheet tab
5. Creates the tab and headers when necessary
6. Checks for duplicate invoices using file hashes
7. Flags duplicate invoices for review
8. Appends the processed invoice to Google Sheets

## Workflow

```text
Google Drive
     │
     ▼
Detect New Invoice
     │
     ▼
Download PDF
     │
     ▼
Extract PDF Text
     │
     ▼
LLM Invoice Extraction
     │
     ▼
Determine Monthly Tab
     │
     ├── Tab exists ──────────────┐
     │                            │
     └── Create tab + headers ────┤
                                  ▼
                           Prepare Row
                                  │
                                  ▼
                         Check File Hash
                                  │
                       ┌──────────┴──────────┐
                       ▼                     ▼
                 Duplicate              New Invoice
                       │                     │
                       ▼                     ▼
                 Flag for Review       Normal Status
                       │                     │
                       └──────────┬──────────┘
                                  ▼
                         Append to Sheet
```

## Key Features

### Automated PDF Processing
Monitors a Google Drive folder for newly created invoice files and extracts text automatically.

### LLM-Based Data Extraction
Uses an LLM to extract structured information from unstructured invoice documents, including:

- Invoice number
- Company name
- Description
- Payee account number
- Bank name
- Biller code
- JomPay reference
- Amount
- Invoice date
- Due date

### Dynamic Monthly Organization
Invoices are automatically routed to monthly Google Sheets tabs based on the invoice date.

Example:

```text
AUG26
SEP26
OCT26
```

If the required monthly tab does not exist, the workflow creates it and initializes the column headers.

### Duplicate Detection
Uses the source file's MD5 hash to identify invoices that have already been processed.

Duplicate files are not silently treated as new invoices. Instead, they are marked:

```text
DUPLICATE - REVIEW
```

This allows the finance team to review potential duplicates before taking further action.

## Output Fields

The workflow records structured invoice information including:

| Field | Description |
|---|---|
| Invoice Number | Invoice/document reference |
| Company Name | Invoice issuer |
| Description | Invoice/service description |
| Email Subject | Original email subject metadata |
| Payee Account Number | Payment account |
| Payee Bank Name | Bank name |
| Biller Code | JomPay biller code |
| JomPay Ref | JomPay reference |
| Amount | Invoice amount |
| Invoice Date | Invoice issue date |
| Due Date | Payment due date |
| Status | Processing / duplicate status |
| Source File Link | Link to source file |
| File Hash | Duplicate detection key |

## Tech Stack

- n8n
- Google Drive
- Google Sheets
- LLM / OpenRouter
- JavaScript expressions
- PDF text extraction

## Repository Structure

```text
invoice-processing-automation/
├── README.md
├── workflow/
│   └── invoice-automation.json
├── screenshots/
│   └── workflow-overview.png
└── .gitignore
```

## Setup

1. Import `workflow/invoice-automation.json` into n8n.
2. Configure your own Google Drive credentials.
3. Configure your own Google Sheets credentials.
4. Configure your own LLM provider credentials.
5. Replace the placeholder Google Drive folder ID.
6. Replace the placeholder Google Sheets document ID.
7. Update the workflow according to your own Google Workspace environment.

> Credentials and resource IDs are intentionally excluded from this repository.

## Security

This repository contains a sanitized version of the workflow.

The following sensitive configuration has been removed or replaced with placeholders:

- API credentials
- OAuth credential IDs
- Google Drive folder IDs
- Google Sheets document IDs
- n8n instance metadata
- Workflow-specific identifiers

No production invoice documents or financial records are included.

## What I Learned

This project involved more than simply connecting AI to a workflow. Key implementation considerations included:

- Handling unstructured PDF invoice data
- Designing structured LLM extraction fields
- Handling ambiguous invoice date formats
- Dynamically creating monthly spreadsheet tabs
- Preventing duplicate processing with file hashes
- Mapping extracted data into a consistent spreadsheet schema
- Designing an automation that can be monitored and reviewed by non-technical users
