# CamScanner Skills

A collection of AI agent skills for document conversion, powered by the [CamScanner AI Tools API](https://ai-tools.camscanner.com).

These skills enable any AI agent (Claude Code, Cursor, Windsurf, etc.) to convert images and PDFs into editable document formats (Word, Excel, TXT, Markdown) through natural language commands. The API is currently **free** to use.

## Available Skills

| Skill | Description | Supported Targets |
| ----- | ----------- | ----------------- |
| [CamScanner-OCR](skills/CamScanner-OCR/SKILL.md) | Convert images (PNG, JPG, etc.) to documents. Also auto-triggers when input images contain text/tables/code — converts to Markdown first for better understanding. | Word, Excel, TXT, Markdown |
| [CamScanner-PDF-Converter](skills/CamScanner-PDF-Converter/SKILL.md) | Convert PDF files to documents. | Word, Excel, TXT, Markdown |

## Install

```bash
npx skills add camscanner-ai/camscanner-skills
```

## Examples

Once installed, just ask your AI agent in natural language:

- "Convert this screenshot to a Word document"
- "Extract text from image.png"
- "Convert report.pdf to Excel"
- "Turn this PDF into Markdown"

The agent will automatically select the appropriate skill and execute the 3-step pipeline (upload → convert → download).

## How It Works

Each skill calls the CamScanner AI Tools API (`https://ai-tools.camscanner.com`) with a 3-step pipeline:

1. **Upload** — Send the source file to the API and receive a `file_id`
2. **Convert** — Request conversion with the source `file_id` and desired target format
3. **Download** — Retrieve the converted file using the output `file_id`

## Limitations

- Requires `curl` and `jq` to be available in the shell environment.

## License

[Apache License 2.0](LICENSE)
