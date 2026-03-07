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
