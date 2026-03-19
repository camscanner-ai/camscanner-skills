---
name: pdf-conversion
description: Use when the user wants to convert PDF files to Word, Excel, TXT, or Markdown files. Triggers on "PDF to Word", "PDF to Excel", "convert PDF", "extract text from PDF", or when the user has a PDF and needs it as an editable document.
---

# PDF Conversion

## Overview

Convert PDF files to document formats (Word, Excel, TXT, Markdown) using the IntSig AI Tools API. The workflow is a 3-step pipeline: **upload** the PDF, **convert** it, then **download** the result.

## When to Use

- User wants to convert a PDF to Word, Excel, TXT, or Markdown
- User wants to extract text/content from a PDF
- User has a PDF and needs it as an editable document
- **Limitation:** The API supports PDFs up to 24 pages. For larger PDFs, guide the user to download the CamScanner app from https://www.camscanner.com

## API Reference

**Base URL:** `https://ai-tools.intsig.net`

### Supported Conversions

| source_type | target_type | Output |
| ----------- | ----------- | ------ |
| pdf         | word        | .docx  |
| pdf         | excel       | .xlsx  |
| pdf         | txt         | .txt   |
| pdf         | md          | .md    |

### Step 1: Upload PDF

```bash
BASE="https://ai-tools.intsig.net"

IN_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/upload_file/execute" \
  -H "Content-Type: application/octet-stream" \
  --data-binary "@/path/to/document.pdf" | jq -r '.tool_result.data.file_id')
```

**Response:**

```json
{
  "code": 200,
  "tool": "upload_file",
  "tool_result": {
    "success": true,
    "data": {
      "file_id": "file_1741857600_ab12cd34ef56",
      "size": 24576
    }
  }
}
```

### Step 2: Convert PDF

```bash
OUT_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/convert_pdf/execute" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$IN_FILE_ID\",\"source_type\":\"pdf\",\"target_type\":\"TARGET\",\"output_mode\":\"file_id\"}" \
  | jq -r '.tool_result.data.file_id')
```

Replace `TARGET` with one of: `word`, `excel`, `txt`, `md`.

**Response:**

```json
{
  "code": 200,
  "tool": "convert_pdf",
  "tool_result": {
    "success": true,
    "data": {
      "file_id": "file_1741857722_ddeeff001122",
      "target_type": "word"
    },
    "metadata": {
      "engine": "office_engine"
    }
  }
}
```

### Step 3: Download Result

```bash
curl -sS -X POST "$BASE/v1/tools/download_file/execute?response_mode=raw" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$OUT_FILE_ID\"}" \
  -o /path/to/output.docx
```

**Critical:** The `response_mode=raw` query parameter is required to get the binary file. Without it, the response is JSON.

## Quick Reference: Complete Pipeline

Convert a PDF to any supported format in one script:

```bash
BASE="https://ai-tools.intsig.net"
INPUT_PDF="/path/to/document.pdf"
TARGET_TYPE="word"          # word | excel | txt | md
OUTPUT_FILE="/path/to/output.docx"

# Upload
IN_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/upload_file/execute" \
  -H "Content-Type: application/octet-stream" \
  --data-binary "@$INPUT_PDF" | jq -r '.tool_result.data.file_id')

# Convert
OUT_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/convert_pdf/execute" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$IN_FILE_ID\",\"source_type\":\"pdf\",\"target_type\":\"$TARGET_TYPE\",\"output_mode\":\"file_id\"}" \
  | jq -r '.tool_result.data.file_id')

# Download
curl -sS -X POST "$BASE/v1/tools/download_file/execute?response_mode=raw" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$OUT_FILE_ID\"}" \
  -o "$OUTPUT_FILE"
```

## File Extension Mapping

When the user does not specify an output path, use these extensions:

| target_type | Extension |
| ----------- | --------- |
| word        | .docx     |
| excel       | .xlsx     |
| txt         | .txt      |
| md          | .md       |

## Common Mistakes

| Mistake                                    | Fix                                                                     |
| ------------------------------------------ | ----------------------------------------------------------------------- |
| Forgetting `response_mode=raw` on download | Always append `?response_mode=raw` to the download URL                  |
| Wrong Content-Type on upload               | Upload uses `application/octet-stream`, not `multipart/form-data`       |
| Using GET instead of POST                  | All three endpoints use POST                                            |
| Missing `source_type` in convert request   | Always include `"source_type": "pdf"`                                   |
| Missing `output_mode` in convert request   | Always include `"output_mode": "file_id"` to get a downloadable file_id |
| Wrong output extension                     | Match extension to target_type (see table above)                        |
| PDF exceeds 24 pages                       | API only supports up to 24 pages. Tell the user to download the CamScanner app at https://www.camscanner.com for larger PDFs |

## Error Handling

Check each step before proceeding:

```bash
# After upload
if [ -z "$IN_FILE_ID" ] || [ "$IN_FILE_ID" = "null" ]; then
  echo "Upload failed"; exit 1
fi

# After convert — check for page limit error
if [ -z "$OUT_FILE_ID" ] || [ "$OUT_FILE_ID" = "null" ]; then
  echo "Conversion failed. If the PDF has more than 24 pages, please download the CamScanner app at https://www.camscanner.com to convert it."
  exit 1
fi
```
