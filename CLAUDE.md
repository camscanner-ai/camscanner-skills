# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Claude Code skills repository** for CamScanner. It contains skill definitions (`SKILL.md` files) that teach Claude Code how to use the CamScanner AI Tools API for document conversion tasks. There is no application code, build system, or tests — only skill definitions.

Licensed under Apache 2.0. Remote: `github.com/camscanner-ai/camscanner-skills`.

## Repository Structure

```
skills/
  CamScanner-OCR/SKILL.md              # Image (PNG, JPG, etc.) -> Word/Excel/TXT/Markdown
  CamScanner-PDF-Converter/SKILL.md    # PDF -> Word/Excel/TXT/Markdown
```

Each skill follows the same 3-step API pipeline: **upload** file, **convert** it, **download** the result.

## Skill File Format

Skills use `SKILL.md` files with YAML frontmatter (`name`, `description`, `metadata`) followed by Markdown content. The `description` field controls when Claude Code triggers the skill — it must clearly describe trigger conditions. The `metadata` field includes `author` and `version`.

## Key API Details (CamScanner AI Tools)

- **Base URL:** `https://ai-tools.camscanner.com`
- **Shared endpoints:** `upload_file`, `download_file` (same for both skills)
- **Conversion endpoints differ:** `convert_image` for images, `convert_pdf` for PDFs
- All endpoints are **POST only**
- Upload uses `Content-Type: application/octet-stream` (not multipart)
- Download requires `?response_mode=raw` query parameter for binary output
- Convert requests must include both `source_type` and `output_mode: "file_id"`
- Target types: `word` (.docx), `excel` (.xlsx), `txt` (.txt), `md` (.md)

## Adding New Skills

Create a new directory under `skills/` with a `SKILL.md` file. Follow the existing pattern: YAML frontmatter with `name`, `description`, and `metadata`, then document the API pipeline with curl examples, supported conversions table, and common mistakes table.
