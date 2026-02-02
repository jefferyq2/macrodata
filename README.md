# Macrodata

Memory infrastructure for AI coding agents. Give Claude Code persistent memory across sessions with journal logging, semantic search, and scheduled reminders.

## Quick Start (Local)

The local plugin runs entirely on your machine with no external dependencies.

**Prerequisites:** [Bun](https://bun.sh) runtime

```bash
/plugin marketplace add ascorbic/macrodata
/plugin install macrodata@macrodata
```

On first run, the agent will guide you through setting up your identity and preferences.

## Features

- **Session Context** - Identity, state, and recent history injected on session start
- **Journal** - Append-only log for observations, decisions, and learnings
- **Semantic Search** - Vector search across your journal and entity files using Transformers.js
- **Scheduled Reminders** - Cron-based recurring and one-shot reminders
- **Entity Files** - Track people, projects, and other entities as markdown files

## Architecture

Macrodata has two modes: **local** (file-based, fully offline) and **cloud** (hosted, WIP).

```
macrodata/
├── plugins/
│   ├── local/            # Local file-based memory (recommended)
│   └── cloud/            # Cloud-hosted memory (WIP)
└── workers/
    └── macrodata/        # Cloudflare Worker for cloud mode (WIP)
```

### Local Mode

All state stored as markdown/JSONL files in `~/.config/macrodata/`:

```
~/.config/macrodata/
├── identity.md           # Agent persona
├── state/
│   ├── human.md          # Your profile and preferences
│   ├── today.md          # Daily focus
│   └── workspace.md      # Active projects
├── entities/
│   ├── people/           # One file per person
│   └── projects/         # One file per project
├── journal/              # JSONL, date-partitioned
└── .index/               # Vectra embeddings index
```

### Cloud Mode (WIP)

Cloud mode provides self-hosted multi-device sync, web search, and background AI processing via a Cloudflare Worker. Documentation in `workers/macrodata/` and `plugins/cloud/`.

## MCP Tools

The local plugin provides these MCP tools:

| Tool | Purpose |
|------|---------|
| `get_context` | Session bootstrap - returns identity, state, journal, schedules |
| `log_journal` | Append timestamped entry (auto-indexed for search) |
| `get_recent_journal` | Get N most recent entries |
| `search_memory` | Semantic search across journal and entities |
| `schedule_reminder` | Create recurring reminder (cron) |
| `schedule_once` | Create one-shot reminder |
| `list_reminders` | List active schedules |
| `remove_reminder` | Delete a reminder |

State and entity files are read/written using Claude Code's built-in filesystem tools.

## Configuration

To use a custom storage directory, create `~/.claude/macrodata.json`:

```json
{
  "root": "/path/to/your/macrodata"
}
```

Default location is `~/.config/macrodata`.

## License

MIT
