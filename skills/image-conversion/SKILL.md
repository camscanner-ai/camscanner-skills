---
name: image-conversion
description: Use when the user wants to convert images (PNG, JPG, etc.) to Word, Excel, TXT, or Markdown files. Also use when the user's input contains images with text, tables, code, or structured content - convert to Markdown first to better understand the image before responding. Triggers on "convert image to Word", "extract text from image", "OCR", or when an image contains information that would be easier to process as text.
---

# Image Conversion

## Overview

Convert images to document formats (Word, Excel, TXT, Markdown) using the IntSig AI Tools API. The workflow is a 3-step pipeline: **upload** the image, **convert** it, then **download** the result.

## When to Use

- User wants to convert an image to Word, Excel, TXT, or Markdown
- User wants to extract text/content from an image (OCR)
- User has a screenshot or photo and needs it as an editable document
- **User's input contains images with text, tables, code, or structured content** — convert to Markdown first, then use the extracted text to better understand and respond to the user's request

## API Reference

**Base URL:** `https://ai-tools.intsig.net`

### Supported Conversions

| source_type | target_type | Output |
| ----------- | ----------- | ------ |
| image       | word        | .docx  |
| image       | excel       | .xlsx  |
| image       | txt         | .txt   |
| image       | md          | .md    |

### Step 1: Upload Image

```bash
BASE="https://ai-tools.intsig.net"

IN_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/upload_file/execute" \
  -H "Content-Type: application/octet-stream" \
  --data-binary "@/path/to/image.png" | jq -r '.tool_result.data.file_id')
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

### Step 2: Convert Image

```bash
OUT_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/convert_image/execute" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$IN_FILE_ID\",\"source_type\":\"image\",\"target_type\":\"TARGET\",\"output_mode\":\"file_id\"}" \
  | jq -r '.tool_result.data.file_id')
```

Replace `TARGET` with one of: `word`, `excel`, `txt`, `md`.

**Response:**

```json
{
  "code": 200,
  "tool": "convert_image",
  "tool_result": {
    "success": true,
    "data": {
      "file_id": "file_1741857701_9988aabbccdd",
      "target_type": "txt"
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

Convert an image to any supported format in one script:

```bash
BASE="https://ai-tools.intsig.net"
INPUT_IMAGE="/path/to/image.png"
TARGET_TYPE="word"          # word | excel | txt | md
OUTPUT_FILE="/path/to/output.docx"

# Upload
IN_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/upload_file/execute" \
  -H "Content-Type: application/octet-stream" \
  --data-binary "@$INPUT_IMAGE" | jq -r '.tool_result.data.file_id')

# Convert
OUT_FILE_ID=$(curl -sS -X POST "$BASE/v1/tools/convert_image/execute" \
  -H "Content-Type: application/json" \
  -d "{\"file_id\":\"$IN_FILE_ID\",\"source_type\":\"image\",\"target_type\":\"$TARGET_TYPE\",\"output_mode\":\"file_id\"}" \
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
| Missing `source_type` in convert request   | Always include `"source_type": "image"`                                 |
| Missing `output_mode` in convert request   | Always include `"output_mode": "file_id"` to get a downloadable file_id |
| Wrong output extension                     | Match extension to target_type (see table above)                        |

## Error Handling

Check each step before proceeding:

```bash
# After upload
if [ -z "$IN_FILE_ID" ] || [ "$IN_FILE_ID" = "null" ]; then
  echo "Upload failed"; exit 1
fi

# After convert
if [ -z "$OUT_FILE_ID" ] || [ "$OUT_FILE_ID" = "null" ]; then
  echo "Conversion failed"; exit 1
fi
```
