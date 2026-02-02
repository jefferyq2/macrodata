# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Structure

```
macrodata/
├── plugins/
│   ├── local/                  # Local file-based memory plugin (primary)
│   │   ├── .claude-plugin/     # Plugin metadata
│   │   ├── bin/                # Daemon and hook scripts
│   │   ├── hooks/              # Plugin hooks config
│   │   ├── skills/             # Plugin skills (e.g., onboarding)
│   │   └── src/                # MCP server source
│   │       ├── index.ts        # MCP server with tool definitions
│   │       ├── indexer.ts      # Vectra indexing logic
│   │       └── embeddings.ts   # Transformers.js embeddings
│   └── cloud/                  # Cloud plugin (WIP)
│       ├── .claude-plugin/
│       ├── bin/
│       └── hooks/
├── workers/                    # Cloudflare Workers (WIP)
│   └── macrodata/              # Cloud memory MCP server
├── package.json                # Root package.json
└── marketplace.json            # Plugin marketplace config
```

## Build and Development Commands

```bash
# Root (for cloud worker)
pnpm dev            # Start local dev server with wrangler
pnpm deploy         # Deploy to Cloudflare Workers
pnpm check          # Type check with tsc
pnpm lint           # Run oxlint
pnpm lint:fix       # Run oxlint with auto-fix
pnpm format         # Format with oxfmt
pnpm test           # Run tests
```

## Architecture

Macrodata provides persistent memory for AI coding agents. Two modes:

### Local Mode (Primary)

**local** (`plugins/local/`) - File-based memory, fully offline.

- `src/index.ts` - MCP server with 11 tools (get_context, log_journal, search_memory, etc.)
- `src/indexer.ts` - Vectra-based vector index for semantic search
- `src/embeddings.ts` - Transformers.js embedding generation (BGE model)
- `bin/macrodata-daemon.ts` - Background daemon for scheduled reminders

**Storage** (default `~/.config/macrodata/`):
- `identity.md` - Agent persona
- `state/` - Current state (human.md, today.md, workspace.md)
- `entities/` - People, projects as markdown files
- `journal/` - JSONL entries, date-partitioned
- `.index/` - Vectra embeddings cache

### Cloud Mode (WIP)

**cloud** (`plugins/cloud/`) - Connects to hosted macrodata service.
**macrodata** (`workers/macrodata/`) - Cloudflare Worker with Durable Objects.

Cloud mode adds: multi-device sync, web search (Brave), background AI processing, external model routing via AI Gateway. See `workers/macrodata/README.md` for details.
