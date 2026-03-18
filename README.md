# Submission Checker

A command-line tool for academic conference submissions that automatically validates PDFs for compliance with conference policies.

## Features

- **Page limit checking** – Warn when the number of pages exceeds a configurable limit.
- **References position** – Detect if references start after the allowed page.
- **Content validation** – Flag occurrences of figures, tables, and appendices on pages that should contain references only.
- **Style conformance** – Verify conformance to ACM or IEEE citation style.
- **Anonymity checks** – Detect non-anonymous emails mentioned on page 1.
- **Suspicious wording** – Identify potentially revealing phrases like "our previous paper [3]".
- **Metadata inspection** – Inspect PDF metadata for possible author information that could reveal identity.

## Installation

```bash
cd submission-checker
pip install -e .
pip install PyPDF2
```

Or use `requirements.txt`:
```bash
pip install -r requirements.txt
pip install -e .
```

## Usage

### Check a Single PDF

```bash
submission-checker --file paper.pdf --max-pages 8 --style acm
```

Output (if issues found):
```
Warnings:
 - Number of pages (10) exceeds limit (8).
 - Non-anonymous email detected on page 1.
 - Suspicious wording detected: 'our previous paper'.
```

Exit code: **0** if no issues, **1** if warnings found.

### Scan a Folder of PDFs

Check all PDFs in a directory:

```bash
submission-checker --folder submissions --max-pages 8 --style acm
```

Output:
```
Filename                                 Status     Issues
======================================================================
paper1.pdf                               ✓ PASS     0
paper2.pdf                               ✗ FAIL     2
  - Number of pages (10) exceeds limit (8).
  - Non-anonymous email detected on page 1.
paper3.pdf                               ✓ PASS     0

======================================================================
Summary: 2 passed, 1 failed out of 3 files
```

## Command-Line Options

- `--file <path>` – Check a single PDF file
- `--folder <path>` – Check all PDFs in a folder (recursive search for `*.pdf`)
- `--max-pages <num>` – Page limit for the submission (optional)
- `--style {acm,ieee}` – Enforce a specific citation style (optional)

**Note:** Provide either `--file` or `--folder`, not both.

## Examples

```bash
# Single file, no style requirement
submission-checker --file paper.pdf --max-pages 10

# Single file, ACM style required
submission-checker --file paper.pdf --max-pages 8 --style acm

# Batch check IEEE submissions
submission-checker --folder submissions --max-pages 12 --style ieee

# Check folder without page limit
submission-checker --folder papers
```

## Running Tests

```bash
pytest tests/test_checker.py -q
```

With verbose output:
```bash
pytest tests/test_checker.py -v -s
```

## How It Works

1. **Text Extraction** – Extracts text from each page of the PDF
2. **Content Analysis** – Scans for patterns, emails, suspicious phrases
3. **Metadata Inspection** – Checks PDF metadata for identifying information
4. **Style Detection** – Analyzes text for ACM/IEEE keywords
5. **Reporting** – Lists all issues found with line-by-line details
