# cc-convo-explorer

A terminal UI for browsing, searching, resuming, and analyzing your Claude Code, Codex, and Pi conversation history.

It auto-discovers Claude Code sessions from `~/.claude/projects/`, Codex sessions from `~/.codex/sessions/`, and Pi sessions from `~/.pi/agent/sessions/`, then gives you a searchable interface across every project you've worked on.

## Screenshot

```
 cc-convo-explorer
 ┌─ PROJECTS (618) ───────────────┐┌─ PREVIEW (25 turns) ──────────────────────┐
 │ Filter convos... (Enter=search) ││                                            │
 │                                  ││ ## ticklish-twirling-hejlsberg             │
 │ ▼ Claude Code  (580)            ││ Date: 2026-04-06 21:11                     │
 │   ▼ ~/Work                      ││ CWD: ~/Work/my-project                     │
 │     ● my-project    (12) ★     ││ ──────────────────────────────────────────  │
 │     ► web-app       (36)        ││ ## User                                    │
 │     ► api-server   (116) ★     ││ would like to make this more profesh...    │
 │     ► cli-tool       (6)        ││                                            │
 │   ► ~                           ││ ## Assistant                               │
 │ ► Codex  (23)                   ││ Let me explore the codebase first...       │
 │ ► Pi  (15)                      ││                                            │
 │                                  ││                                            │
 ├──────────────────────────────────┤│                                            │
 │ 12 projects · 618 conversations ││                                            │
 └──────────────────────────────────┘└────────────────────────────────────────────┘
```

## Features

- **Browse all projects** — auto-discovers Claude Code, Codex, and Pi sessions
- **Agent-grouped tree** — top-level nodes per agent (colored), with path groups and projects underneath
- **Search/filter** — type to filter instantly, press Enter for deep full-text search
- **Resume & handoff** — press `R` to resume or `H` to hand off context to a new session (all agents)
- **Preview** — select any conversation to see the full user/assistant exchange
- **Multi-select** — select individual conversations, entire projects, or everything
- **Token estimation** — see estimated token count for selected conversations
- **Export** — export individual conversations or combined multi-conversation markdown
- **Gemini analysis** (optional) — analyze conversations with Google Gemini to extract patterns, preferences, and insights
- **Model picker** — cycle between Gemini models
- **Editable prompts** — customize the analysis prompt before running
- **Analyzed indicators** — projects that have been analyzed show a ★ marker
- **Resizable sidebar** — drag the divider to resize
- **CLI mode** — list, search, export, resume, and analyze without the TUI

## Install

```bash
# Install globally (recommended)
uv tool install "cc-convo-explorer[ai] @ git+https://github.com/testy-cool/cc-convo-explorer.git"

# Or clone and install locally
git clone https://github.com/testy-cool/cc-convo-explorer.git
cd cc-convo-explorer
uv sync --extra ai
```

Requires Python 3.12+ and [uv](https://docs.astral.sh/uv/).

## Usage

### TUI (interactive)

```bash
cc-convo-explorer
```

**Keyboard shortcuts:**

| Key | Action |
|-----|--------|
| `/` | Focus search/filter |
| `Enter` | Deep search (in filter) / Preview conversation (in tree) |
| `Esc` | Clear filter / Cancel analysis |
| `S` | Toggle select on current item |
| `Ctrl+A` | Select all |
| `Ctrl+D` | Deselect all |
| `R` | Resume conversation (Claude Code, Codex) |
| `H` | Handoff context to a new session |
| `E` | Export selected as individual markdown files |
| `C` | Export selected as one combined markdown file |
| `A` | Analyze with Gemini |
| `M` | Cycle Gemini model |
| `P` | Edit analysis prompt |
| `O` | Open exports/analyses folder |
| `Tab` | Switch focus between sidebar and preview |
| `Q` | Quit |

### CLI (headless)

```bash
# List all projects and conversations
cc-convo-explorer --list

# Search across all conversations
cc-convo-explorer --search "auth middleware"

# Resume a conversation by slug or UUID
cc-convo-explorer --resume reflective-herding-biscuit
cc-convo-explorer --resume 019e4488

# Handoff to a new session (exports context, launches new CLI)
cc-convo-explorer --handoff              # latest conversation in cwd
cc-convo-explorer --handoff select       # pick from list
cc-convo-explorer --handoff codex        # latest Codex conversation in cwd

# Export by file path, UUID prefix, or slug
cc-convo-explorer --concat path/to/session.jsonl
cc-convo-explorer --concat 315ce500
cc-convo-explorer --concat reflective-herding-biscuit

# Analyze with Gemini
export GEMINI_API_KEY=your-key-here
cc-convo-explorer --analyze 315ce5 reflective-herding --model gemini-3.1-pro-preview

# Custom analysis prompt (inline or from file)
cc-convo-explorer --analyze 315ce5 --prompt "List all tools used.\n\n{content}"
cc-convo-explorer --analyze 315ce5 --prompt my-prompt.txt

# Detail levels: text (default), tools, results, full
cc-convo-explorer --concat 315ce5 --detail tools     # +tool call summaries
cc-convo-explorer --concat 315ce5 --detail results   # +truncated tool output
cc-convo-explorer --concat 315ce5 --detail full      # +everything untruncated
```

#### Detail levels

| Level | What's included | Typical overhead |
|-------|----------------|-----------------|
| `text` | User/assistant text only | baseline |
| `tools` | + tool call summaries (Bash commands, file edits, greps) | +20-30% |
| `results` | + truncated tool output (500 chars each) | +80-100% |
| `full` | + full untruncated tool output | +300-2000% |

Exports include a stats header: model, token count, duration, tool calls, and estimated cost.

## Gemini Analysis

Set your API key:

```bash
# Environment variable
export GEMINI_API_KEY=your-key-here

# Or .env file in project directory
echo GEMINI_API_KEY=your-key-here > .env
```

Get a free key at [aistudio.google.com](https://aistudio.google.com/apikey).

Analysis extracts:
- Key decisions and their rationale
- User preferences and workflow patterns
- Problems encountered and solutions
- Recurring patterns across sessions
- Unfinished work and TODOs

Results saved to `~/.claude/convo-explorer/analyses/`.

For multi-conversation analysis, select multiple items and press `A` — Gemini finds cross-session patterns and preference evolution.

## File locations

| What | Where |
|------|-------|
| Claude Code logs | `~/.claude/projects/{project}/*.jsonl` |
| Codex logs | `~/.codex/sessions/YYYY/MM/DD/*.jsonl` |
| Pi logs | `~/.pi/agent/sessions/**/*.jsonl` |
| Analyses | `~/.claude/convo-explorer/analyses/` |
| Combined exports | `~/.claude/convo-explorer/exports/` |

## License

MIT
