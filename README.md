# ClaudeTricks

A collection of tricks, tips, and configurations I've found useful when working with Claude Code.

---

## Context Window Status Line

**File:** `statusline-guide.md`

A 10-dot context usage indicator for the Claude Code status bar. Each dot represents 10% of the context window used, with color shifting green → yellow → red as you approach the limit.

**Preview:** `●●●○○○○○○○ 30%`

See [`statusline-guide.md`](statusline-guide.md) for full setup instructions (2 steps, ~2 minutes).

**Requirements:** Claude Code CLI, `jq` (`brew install jq`), ANSI-capable terminal.

---

## Context Mode

**Repo:** [mksglu/claude-context-mode](https://github.com/mksglu/claude-context-mode)

An MCP server that acts as a virtualization layer for Claude Code's context window. It intercepts tool calls that would dump large raw data into context, runs them in a sandbox instead, and returns only a compact summary — while separately tracking session state so the model picks up where it left off after compaction.

**Benefits:**

- **Massive context savings** — raw tool output stays in a sandbox; a 315 KB result becomes 5.4 KB (up to 98% reduction), so your context window lasts far longer
- **Session continuity after compaction** — file edits, tasks, errors, and decisions are persisted in SQLite and restored via BM25 search, so the model doesn't lose track of what it was doing
- **Drop-in install** — one `/plugin` command sets up the MCP server, hooks, and routing instructions automatically; no manual configuration needed

---

## LearnSync — /learn and /sync Skills

**File:** `learnsync.md`

A single setup prompt that installs two slash-command skills and wires them together into a system for making Claude smarter over time — without bloating `CLAUDE.md`.

**The problem it solves:** The obvious approach (writing every preference and learning directly into `CLAUDE.md`) creates a 4,000-line instruction file that becomes unmanageable. This approach routes learnings to your Obsidian vault instead, keeping `CLAUDE.md` slim while Claude gets smarter through proactive vault searches.

**`/learn`** — Run at the end of any session. Extracts durable preferences, tool discoveries, corrections, and workflow patterns from the conversation and writes them as structured notes to your Obsidian vault. `CLAUDE.md` only gets updated with *search triggers* (new topic categories to search for), never the actual knowledge.

**`/sync`** — Run before `/clear`. Saves full project state — what was completed, what's next, open decisions, key gotchas — to `~/.claude/projects/[slug]/memory/MEMORY.md` so the next session picks up exactly where this one left off.

**How it compounds:** Because learnings live in the vault and `CLAUDE.md` tells Claude to search it proactively, context accumulates naturally. Each session Claude searches relevant past notes before starting — no manual prompting required.

See [`learnsync.md`](learnsync.md) for the full setup prompt (customize `YOUR_VAULT_PATH` before pasting).

**Prerequisites:** Before setting up these skills, connect Claude Code to your Obsidian vault. Watch [this setup walkthrough](https://www.youtube.com/watch?v=eRr2rTKriDM) first — it covers the MCP server configuration that lets Claude read and write vault notes. Once that's working, the LearnSync prompt will wire the skills on top of it.

**Requirements:** Obsidian vault connected to Claude Code (via MCP), Claude Code CLI.

---

## Superpowers

**Repo:** [obra/superpowers](https://github.com/obra/superpowers)

An agentic skills framework and software development methodology for Claude Code (and other AI coding agents). Rather than letting the agent immediately jump into writing code, Superpowers intercepts the moment you start building something and guides the agent through a structured workflow: clarify what you're actually trying to build, produce a readable spec, generate a clear implementation plan, then execute it via subagent-driven development — with TDD and review baked in throughout.

Skills trigger automatically based on what you're doing. You don't invoke them manually; the agent just picks them up.

**Built-in skills library:**

- **test-driven-development** — enforces RED-GREEN-REFACTOR cycle with anti-patterns reference
- **systematic-debugging** — 4-phase root cause process (root-cause tracing, defense-in-depth, condition-based waiting)
- **verification-before-completion** — confirms issues are actually fixed before declaring done
- **brainstorming** — Socratic design refinement before writing a line of code
- **writing-plans / executing-plans** — detailed implementation plans with batch execution and checkpoints
- **dispatching-parallel-agents** — concurrent subagent workflows for independent tasks
- **subagent-driven-development** — fast iteration with two-stage review (spec compliance, then code quality)
- **requesting/receiving-code-review** — structured pre-review checklist and feedback response workflow
- **using-git-worktrees** — parallel development branch management
- **writing-skills** — create new skills following best practices

**Benefits:**

- **Autonomous long runs** — agents can work for hours without deviating from the plan you approved
- **Better specs upfront** — Socratic design refinement catches ambiguity before code is written
- **Discipline without effort** — TDD, YAGNI, and DRY are enforced by the workflow, not by remembering to ask
- **Extensible** — write your own skills following the same pattern; the framework loads them automatically
- **Multi-platform** — works with Claude Code, Cursor, Codex, and OpenCode
