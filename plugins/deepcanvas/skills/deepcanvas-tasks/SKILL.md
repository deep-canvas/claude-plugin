---
name: deepcanvas-tasks
description: List the user's assigned DeepCanvas tasks for the current project. Use this whenever the user asks to see their tasks, work items, todos, or what they should work on. Recognize these patterns in any language - English examples - "what are my tasks", "show me my tasks", "what should I work on today", "list my work", "what's on my plate", "what do I have to do". Turkish examples - "bugünkü görevlerimi göster", "görevlerimi listele", "bugün ne yapmalıyım", "görevlerim neler", "bana görevlerimi göster". Works through the deep CLI (directory linked via `deep init`) or the DeepCanvas MCP server.
allowed-tools: Bash(deep tasks*)
---

# DeepCanvas: List Tasks

## Which path: deep CLI or DeepCanvas MCP

Two ways reach DeepCanvas; pick once per session and tell the user which one you used only if asked.

- **deep CLI** (`deep` on PATH and `.deep/config.toml` in the repository): use it for `pull` and `done`. `deep pull` records when work started and `deep done` reads the local transcripts, so the task gets its real token and time usage. This is the only way token usage is measured.
- **DeepCanvas MCP** (`deepcanvas` tools such as `list_tasks`, `get_task`, `complete_task` are available): use it when the CLI is not installed or the repository is not bound, and always for things the CLI cannot do (documents, folders, Brief, board edits, comments). `complete_task` records duration and tool calls but not tokens.
- Both available: CLI for pull/done, MCP for everything else. Never run both `deep done` and `complete_task` for the same task.

With the CLI, run once via the Bash tool:

!`deep tasks --headless`

With MCP, call `list_tasks` with `project` = `org/project` from `.deep/config.toml` (or `list_projects` when there is no config). The MCP result is already readable; the CLI result is JSON, parse it.

Parse the JSON output and present the tasks to the user as a readable list or table. Include the task code, title, status, energy level, and linked PRD code.

If the user picks a task from this list (e.g., "let's work on DC-142", "I'll do the first one"), invoke the `deepcanvas-pull` skill with that task code.

If the output is an error (non-zero exit, `ok: false` in JSON), interpret the `error.kind` field:
- `NotAuthenticated` — tell the user to run `deep login` in their terminal
- `NoProjectBinding` — tell the user to run `deep init` to link this directory to a DeepCanvas project
- `Unauthorized` — token may have been revoked; user should run `deep login` again
- Other — show the message verbatim

Do not run any other commands. Do not modify any files. This skill is read-only.
