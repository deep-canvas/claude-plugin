---
name: deepcanvas-done
description: Mark a DeepCanvas task as completed. Use when the user indicates they are finished with a task. Triggers include - English - "I'm done", "I finished", "mark this done", "complete this task", "mark DC-142 as done", "DC-142 is done". Turkish - "bitirdim", "tamamlandı", "DC-142'yi bitirdim", "bu görevi tamamla", "DC-142 tamam". With no explicit task code, this completes the active task (the last one pulled). With an explicit task code, completes that specific task.
argument-hint: [<task-code>]
allowed-tools: Bash(deep done*)
---

# DeepCanvas: Mark Task Done

Run `deep done` **exactly once** using the Bash tool. Never run it a second time in the same turn — the first successful run already completed the task and cleared the local active marker.

- If the user provided a task code in $ARGUMENTS, run: `deep done $ARGUMENTS --headless --verbose`
- If no task code was provided (active task), run: `deep done --headless --verbose`

Always pass `--headless --verbose` so the full output is captured.

From the JSON output:
- On success (`ok: true`):
  - If `already_done: true` — tell the user the task had already been completed earlier (e.g., from the web UI); nothing was changed now.
  - Otherwise — confirm to the user with the task code and title that was completed.
- On error (`ok: false`), interpret `error.kind`:
  - `NoActiveTask` — tell user there's no active task; they need to pull a task first or pass a code explicitly
  - `Api` with 409:
    - message contains `BOARD_HAS_NO_DONE_STATUS` — the board has no "Done" column; the user must add a done-category status on the board first
    - message mentions "already" — task was already marked done (legacy backend)
  - `NotAuthenticated` / `NoProjectBinding` — usual guidance
  - Other — show message verbatim

## After a successful completion: knowledge assessment

Only when the completion succeeded (`ok: true`), assess whether the completed work changed any contract that OTHER projects' agents rely on:

- API endpoints or request/response shapes → kind `api`
- DB tables, columns, constraints → kind `db`
- Published events / queue message payloads → kind `events`
- Shared types, auth flows, design tokens → `auth` / `ui` etc.

If something contract-relevant changed, tell the user which knowledge kind is affected and **propose** updating it (e.g. "Bu task `api` sözleşmesini değiştirdi — knowledge dokümanını güncelleyeyim mi?"). **Never push without explicit user confirmation.** If the user confirms, follow the deepcanvas-knowledge skill's push flow. If nothing contract-relevant changed, do not mention knowledge at all.

Other than this assessment, do not run any other commands afterward unless the user asks.
