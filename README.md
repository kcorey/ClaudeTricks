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

## @claude-code-agent — GitHub Autonomous Agent

**Docs:** [Claude Code GitHub Actions](https://code.claude.com/docs/en/github-actions) | **App:** [github.com/apps/claude](https://github.com/apps/claude)

One of the most underrated Claude Code features: install the Claude GitHub app on any repository and you get an autonomous coding agent you can summon with a simple `@claude` mention in any issue or pull request comment. No terminal needed — Claude reads the request, explores the codebase, writes the code, and opens a pull request, all from within GitHub.

**What it can do:**

- `@claude implement this` on an issue → Claude writes the code and opens a PR for review
- `@claude fix the TypeError in the user dashboard` → Claude diagnoses and patches the bug
- `@claude review this PR for security and correctness` → Claude posts inline review comments
- `@claude add tests for the auth module` → Claude writes and commits a test suite
- Runs on any GitHub event: issue comments, PR review comments, PR opens, scheduled crons

**Benefits:**

- **Zero context switching** — trigger full Claude Code sessions directly from GitHub without opening a terminal
- **Parallel execution** — mention `@claude` on 20 issues and get 20 agents working simultaneously, each in an isolated environment
- **Team-accessible** — any collaborator can invoke Claude, not just the developer with the local setup
- **Respects your project standards** — reads `CLAUDE.md`, MCP servers, and skills automatically
- **Composable** — combine with scheduled triggers, PR auto-review, issue triage, and daily maintenance workflows

**Tips and tricks:**

- Be specific in your `@claude` command — `@claude implement issue #42 using the existing UserService pattern` works better than `@claude fix this`
- Pair it with a `CLAUDE.md` that sets coding standards; the agent reads it before every run
- Use `claude_args: "--max-turns 10"` in your workflow YAML to cap runaway jobs and control costs
- Set `trigger_phrase` to a custom string (e.g., `@claude-review`) if you want separate workflows for different task types
- Add `--max-turns 5` for quick review tasks, higher for complex feature implementation
- Works with AWS Bedrock and Google Vertex AI for enterprise environments — you're not locked into the direct API

**Setup (2 minutes):**

Run `/install-github-app` inside Claude Code terminal — it walks you through installing the GitHub app and setting the `ANTHROPIC_API_KEY` secret. Or install manually from [github.com/apps/claude](https://github.com/apps/claude) and copy the workflow from the [examples directory](https://github.com/anthropics/claude-code-action/tree/main/examples).

**Requirements:** GitHub repository admin access, Anthropic API key (or Bedrock/Vertex credentials), Claude Code CLI (for `/install-github-app` quickstart).

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
