# DeepCanvas Claude Code Plugin

Claude Code skills that drive the DeepCanvas CLI:

- `deepcanvas-tasks` — list assigned tasks
- `deepcanvas-pull` — fetch task context (description + PRDs)
- `deepcanvas-done` — mark task complete

## Prerequisites

Either the deep CLI (measures token and time usage per task from the local transcripts):

```bash
curl -fsSL cli.deepcanvas.studio/install.sh | sh
deep login
deep init
```

or the DeepCanvas MCP server (no install; documents, board and Brief management too):

```bash
claude mcp add --transport http --scope user deepcanvas https://mcp.deepcanvas.studio/mcp
```

Both together is best: the skills use the CLI for pull/done and MCP for the rest.

## Install Plugin

```
/plugin marketplace add deep-canvas/claude-plugin
/plugin install deepcanvas
```

## Usage

Just ask Claude naturally:

- "What are my tasks?" → lists assigned tasks
- "Let's work on DC-142" → pulls task context, reads PRD, suggests plan
- "I'm done" → marks the active task complete

Türkçe de çalışır: "bugünkü görevlerimi göster", "DC-142'yi çekelim", "bitirdim".
