# Claude Code Context Dot Indicator

A 10-dot context usage indicator for the Claude Code status bar. Each dot = 10% of context window used. Color shifts green → yellow → red as you approach the limit.

**Preview:** `●●●○○○○○○○ 30%`

---

## Setup (2 steps)

### 1. Create the script

Save this to `~/.claude/statusline.sh`:

```bash
#!/bin/bash
input=$(cat)
pct=$(echo "$input" | jq -r '.context_window.used_percentage // 0')
pct=${pct%.*}

filled=$(( pct / 10 ))
[ $filled -gt 10 ] && filled=10

if [ $filled -le 5 ]; then
  color="\033[32m"   # green
elif [ $filled -le 7 ]; then
  color="\033[33m"   # yellow
else
  color="\033[31m"   # red
fi
reset="\033[0m"

dots=""
for i in $(seq 1 10); do
  if [ $i -le $filled ]; then
    dots="${dots}●"
  else
    dots="${dots}○"
  fi
done

printf "${color}${dots}${reset} ${pct}%%"
```

Make it executable:

```bash
chmod +x ~/.claude/statusline.sh
```

### 2. Register it in settings

Add the `statusLine` block to `~/.claude/settings.json`:

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

If you already have a `settings.json`, just add the `statusLine` key alongside your existing config.

---

## How it works

Claude Code pipes a JSON blob to your script's stdin after each assistant message. The script reads `.context_window.used_percentage`, maps it to 0–10 filled dots, applies ANSI color, and prints to stdout. Claude Code renders whatever your script prints in the status bar.

**Color thresholds:**
| Usage | Color |
|-------|-------|
| 0–50% | Green |
| 51–70% | Yellow |
| 71–100% | Red |

---

## Customisation ideas

- **Add percentage text** — already included (`30%`)
- **Add cost tracking** — read `.session.total_cost_usd` from the JSON
- **Add git branch** — `git branch --show-current` inside the script
- **Change dot characters** — swap `●`/`○` for `█`/`░` or emoji
- **Use `/statusline` command** — Claude Code can generate a script for you with natural language: `/statusline show context dots and git branch`

---

## Troubleshooting

- **Nothing appears:** Check `jq` is installed (`brew install jq`)
- **Script not found:** Ensure the path in `settings.json` matches exactly, and the file is executable
- **Changes not showing:** The status line updates after the next assistant message — send any message to trigger a refresh
- **`null` percentage early on:** Normal — the field is `null` before the first API call; the `// 0` fallback handles this

---

## Requirements

- Claude Code CLI (not Claude Desktop — see note below)
- `jq` installed (`brew install jq` on macOS)
- A terminal with ANSI color support (iTerm2, Ghostty, Warp, Terminal.app all work)
