# LearnSync Setup Prompt

Paste this into Claude Code to set up `/learn` and `/sync` skills that keep Claude getting smarter over time — **without bloating your CLAUDE.md**.

The strategy: learnings live in your Obsidian vault as structured notes. CLAUDE.md stays slim and just tells Claude to search the vault proactively. Over time, Claude accumulates context through natural vault searches rather than an ever-growing instruction file.

**Before pasting:** Replace `YOUR_VAULT_PATH` with the absolute path to your Obsidian vault (e.g. `/Users/yourname/Documents/Obsidian Vault`).

---

```
Do the following exactly, in order. No confirmation needed — just execute all steps.

## Step 1: Create the /learn skill

Write this file to ~/.claude/skills/learn/SKILL.md:

---
name: learn
description: Extract durable learnings from the current conversation and write them to the Obsidian vault as structured notes. Trigger when the user says "/learn", "learn from this", "remember how I work", "take notes on this session", or adds an explicit note like "/learn I prefer X". The goal is continuous improvement without bloating CLAUDE.md — learnings live in the vault where they can be searched proactively.
---

# Learn — Write Session Learnings to Vault

Your job is to extract what's genuinely new and durable from this conversation and file it in the Obsidian vault so future sessions can find it.

## Step 1: Read ~/.claude/CLAUDE.md
Understand what's already captured globally so you don't duplicate it.

## Step 2: Identify learnings worth keeping

Scan the conversation through these lenses:

**Work style & preferences**
- How the user phrases requests; what they expect without asking
- Output preferences, format requirements, file destinations
- Communication style corrections

**Tool & workflow discoveries**
- New tools, paths, CLI commands, workarounds
- Pipelines established this session that should be reusable

**Corrections & feedback** (highest signal)
- Anything the user had to correct or redirect
- Things that required extra back-and-forth to resolve

**Project & business context**
- New projects, goals, domains mentioned
- Skills or workflows created this session

**Explicit notes** from `/learn I prefer X` → incorporate verbatim, high priority.

Filter ruthlessly:
- Include: recurring preferences, tool paths, workflow patterns, corrections
- Skip: one-off filenames, task-specific details, things that will change soon
- Skip: anything already accurately captured in CLAUDE.md

## Step 3: Write to the Obsidian vault

Vault path: `YOUR_VAULT_PATH`

Create one note per distinct topic at `YOUR_VAULT_PATH/AI Learnings/YYYY-MM-DD-[topic-slug].md`

Each note uses this structure:
```
---
title: [descriptive title]
date: YYYY-MM-DD
tags: [ai-learning, relevant-domain-tags]
up:: [[AI Learnings MOC]]
---

# [Title]

## What I Learned
[The actual learning — be specific and concrete]

## When to Apply
[Trigger conditions — when should Claude use this knowledge?]

## Source
Session date: YYYY-MM-DD
```

If multiple learnings belong together, group them in one note. If they're unrelated, write separate notes.

## Step 4: Update CLAUDE.md only for search triggers

CLAUDE.md should stay lean. Only update it if a new *category* of topics should trigger proactive vault searches. Add the topic to the "Proactive vault search" list — don't write the actual knowledge there.

## Step 5: Confirm briefly
- What notes were written (file paths + one-line summary each)
- Anything skipped and why
- Whether CLAUDE.md was updated


## Step 2: Create the /sync skill

Write this file to ~/.claude/skills/sync/SKILL.md:

---
name: sync
description: Full context checkpoint before /clear. Saves project state, decisions, and next steps so the next session picks up exactly where this one left off. Trigger when the user says "/sync", "save context", "checkpoint before clear", or "sync before clearing".
---

# Sync — Context Checkpoint Before /clear

Save everything that matters. The next session will have no memory of this conversation other than what you write now.

## Step 1: Review the conversation
Note: decisions made, work completed, bugs fixed, patterns discovered, open questions, what comes next.

## Step 2: Find or create the project memory directory
Path: `~/.claude/projects/[project-slug]/memory/`
The slug is the current working directory path with `/` replaced by `-` (drop leading `-`).

## Step 3: Write MEMORY.md
Create or update `MEMORY.md` in that directory:

```
# [Project Name] — Context

## Current State
[What was just completed. Be specific.]

## Next
[What comes next. One clear sentence.]

## Open Decisions
[Any unresolved questions or forks in the road.]

## Key Patterns / Gotchas
[Anything that took effort to figure out and will come up again.]

## Last Updated
YYYY-MM-DD
```

## Step 4: Write topic files only if needed
Only create `decisions.md`, `debugging.md`, `patterns.md` etc. if the detail genuinely doesn't fit in MEMORY.md. Don't duplicate.

## Step 5: Write a vault note if project context is broadly useful
If insights from this session apply beyond this project, write a note to `YOUR_VAULT_PATH/AI Learnings/` following the same template as the /learn skill.

## Step 6: Confirm
- Files written (paths)
- What the next session will pick up from
- Open blockers or decisions to be aware of

End with: "All context saved. Run `/clear` to start a fresh session."


## Step 3: Add vault search instruction to CLAUDE.md

Append this section to ~/.claude/CLAUDE.md if it doesn't already have vault search instructions:

```
## AI Learnings

Learnings about how I work are stored in the Obsidian vault at `YOUR_VAULT_PATH/AI Learnings/`.

Before starting any task, search the vault for relevant learnings:
- For preferences about output format, file destinations, communication style
- For known tool paths, CLI workarounds, or pipeline patterns
- For corrections from past sessions

Use Grep on the vault directory to find relevant notes: grep -r "[topic]" "YOUR_VAULT_PATH/AI Learnings/"
```


## Step 4: Confirm everything

After completing all steps, confirm:
- ~/.claude/skills/learn/SKILL.md created ✓
- ~/.claude/skills/sync/SKILL.md created ✓
- ~/.claude/CLAUDE.md updated with vault search section ✓
- Show me the first 5 lines of each SKILL.md to verify

Do not ask for confirmation between steps. Execute all steps now.
```
