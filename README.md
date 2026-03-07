# ClaudeTricks

A collection of tricks, tips, and configurations I've found useful when working with Claude Code.

---

## Context Window Status Line

**File:** `statusline-guide.md`

A 10-dot context usage indicator for the Claude Code status bar. Each dot represents 10% of the context window used, with color shifting green → yellow → red as you approach the limit.

**Preview:** `●●●○○○○○○○ 30%`

See [`statusline-guide.md`](statusline-guide.md) for full setup instructions (2 steps, ~2 minutes).

**Requirements:** Claude Code CLI, `jq` (`brew install jq`), ANSI-capable terminal.
