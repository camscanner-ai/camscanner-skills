# CamScanner Skills

A collection of AI agent skills for document conversion, powered by the [CamScanner AI Tools API](https://ai-tools.camscanner.com).

These skills enable any AI agent (Claude Code, Cursor, Windsurf, etc.) to convert images and PDFs into editable document formats (Word, Excel, Markdown) through natural language commands. The API is currently **free** to use.

## Available Skills

| Skill | Description | Supported Targets |
| ----- | ----------- | ----------------- |
| [CamScanner-PDF2Markdown](skills/CamScanner-PDF2Markdown/SKILL.md) | Convert PDF documents to Markdown with high-precision document parsing. | Markdown |
| [CamScanner-Image2Markdown](skills/CamScanner-Image2Markdown/SKILL.md) | Intelligently recognize image content and convert to Markdown. Also auto-triggers when input images contain text/tables/code. | Markdown |
| [CamScanner-Any2Markdown](skills/CamScanner-Any2Markdown/SKILL.md) | Convert PDF or image files to Markdown with auto-detection of input format. | Markdown |
| [CamScanner-PDF2Office](skills/CamScanner-PDF2Office/SKILL.md) | Convert PDF documents to editable Word or Excel with accurate format preservation. | Word, Excel |
| [CamScanner-Image2Office](skills/CamScanner-Image2Office/SKILL.md) | Recognize image content and convert to editable Word or Excel with high fidelity. | Word, Excel |

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
