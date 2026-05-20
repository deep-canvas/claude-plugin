# DeepCanvas Claude Code Plugin

Claude Code skills that drive the DeepCanvas CLI:

- `deepcanvas-tasks` — list assigned tasks
- `deepcanvas-pull` — fetch task context (description + PRDs)
- `deepcanvas-done` — mark task complete

## Prerequisites

```bash
curl -fsSL cli.deepcanvas.studio/install.sh | sh
deep login
deep init
```

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
