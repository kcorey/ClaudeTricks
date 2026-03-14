# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## Project Purpose

ClaudeTricks is a public reference library of Claude Code configurations, setup guides, and tricks. Content is Markdown-only — no application code. Everything here should be copy-paste-ready and tested before publishing.

**Current guides:**
- `README.md` — overview and linking hub; the entry point for all guides
- `statusline-guide.md` — context window visual indicator (status bar setup)
- `learnsync.md` — `/learn` and `/sync` skill setup prompt

**Repo lives in two places:**
- Primary: `/Volumes/PS2000W/Developer/ClaudeTricks` (external drive)
- Mirror: `/Users/kencorey/Developer/ClaudeTricks`

---

## Documentation Standards

- **Every new trick gets a README section first** — include: one-sentence description, 3–4 benefit bullets, prerequisites, and a link to the detailed guide file if one exists
- **README sections use `---` dividers** for visual separation (existing pattern — maintain it)
- **File naming:** kebab-case, descriptive (e.g., `mcp-server-setup.md`, `hooks-guide.md`)
- **Before committing:** manually vet all copy-paste commands in a clean shell; note the Claude Code version tested against if version-sensitive
- **No code artifacts** — this repo documents Claude Code behavior, not application code; shell commands and config snippets are fine inline

---

## When to Update README

- Adding a new guide file → add a section to README with description, benefits, and link
- Updating an existing guide → verify README description and link still match
- Before any commit → check that all external repo links and tool references are still current

---

## What NOT to Do

- Don't add application code (Python scripts, JS, etc.) unless they are self-contained examples embedded in a guide
- Don't create stub files — only create a guide when it has real content
- Don't modify both primary and mirror locations — git handles sync; only work in the primary path
