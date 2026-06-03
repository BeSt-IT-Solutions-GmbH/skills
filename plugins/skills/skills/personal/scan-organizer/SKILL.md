---
name: scan-organizer
description: Organize scanned PDF documents by reading their content, splitting multi-document scans into individual files, and renaming each according to the pattern YYYYMMDD - Absender - Betreff.pdf. Use when user mentions scanned documents, PDF organizing, scan renaming, Scan-Dateien, or wants to sort/rename scanned files.
---

# Scan Organizer

## Quick Start

1. Find all `Scan*.pdf` files in the target folder
2. Read each PDF's text content
3. Determine if single- or multi-document
4. Split multi-document PDFs, then rename all files as `YYYYMMDD - Absender - Betreff.pdf`

## Workflow

### 1. Find Scan Files

Search the target folder (default: `00_Eingang/Scans`) for files matching `Scan*.pdf`.

### 2. Read & Analyze Each File

For each PDF, extract the text content page by page. From the extracted text, determine:

- **Datum**: The document date (format: YYYYMMDD)
- **Absender**: Who sent/issued the document
- **Betreff**: A short, precise subject description
- **Dokumentgrenzen**: Whether the PDF contains multiple separate documents

**Signs of multiple documents**: Different letterheads, different senders, abrupt topic changes, repeated salutations/headers, or different dates across pages.

### 3. Process Files

**Single document** → Rename directly:

```powershell
Rename-Item "Scan001.pdf" "20260306 - Stadt Kleve - Gewerbesteuerbescheid.pdf"
```

**Multi-document** → Split the PDF into separate files (one per document), then rename each part. After successful splitting and renaming, delete the original scan file.

### 4. Naming Rules

- Format: `YYYYMMDD - Absender - Betreff.pdf`
- Absender and Betreff: so kurz wie sinnvoll, so präzise wie nötig
- Example: `20260306 - Stadt Kleve - Gewerbesteuerbescheid.pdf`

## Rules

- **Always read content first** before deciding on any action
- **Ask the user** (use ask_user) when:
  - Document boundaries are unclear
  - Date, sender, or subject cannot be reliably determined
  - A scan might contain overlapping or ambiguous documents
- **Only rename** files with clearly determinable single documents
- **Mark as multi-part** if reliable technical splitting is not possible
- Process files one at a time and confirm results before moving to the next
