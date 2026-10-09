---
name: deepcanvas-knowledge
description: Publish or fetch cross-project knowledge documents in DeepCanvas. PUSH - use when the user wants to publish or update this project's contract docs for other projects' coding agents. Triggers - English - "publish the api docs", "update project knowledge", "push the db schema knowledge", "share our endpoint docs". Turkish - "knowledge güncelle", "api dokümanını yayınla", "db şemasını paylaş", "bilgiyi merkeze gönder". PULL - use when the user (or a task) needs another project's contract docs. Triggers - English - "check the db schema knowledge", "fetch the backend's api docs", "what endpoints does the api project expose", "list knowledge". Turkish - "backend'in api dokümanını çek", "db projesinin şemasını getir", "knowledge listele", "katalogda ne var". Kinds are short slugs like api, db, events.
argument-hint: [push|pull|list|catalog] [<kind>] [--project org/proj]
allowed-tools: Bash(deep knowledge*), Write, Read, Glob
---

# DeepCanvas: Cross-Project Knowledge

Knowledge documents are contracts shared between projects. Core kinds (reuse these before inventing a new one — check the catalog first):

- `api` — endpoint contracts (paths, methods, request/response shapes, error codes)
- `db` — table schemas (columns, types, constraints, relations)
- `events` — published events / queue message contracts
- `ui` — design system / shared component contracts
- `auth` — authentication flows and token semantics
- `infra` — deploy / environment facts other projects depend on
- `glossary` — domain terms

Run every `deep` command via the Bash tool, exactly once, always with `--headless`. When the deep CLI is not available but the DeepCanvas MCP tools are, use `knowledge_catalog`, `knowledge_get` and `knowledge_push` instead; the flows below map one to one (catalog → list/pull → read, write → push).

## Pull flow (fetching another project's knowledge)

1. Discover first: run `deep knowledge catalog --headless`. Each entry has `project`, `kind`, `title`, `description`, `version`, `local_version`, `stale`.
2. Pick the entries that matter for the current work (judge by `description`). Also re-pull anything relevant with `stale: true` — that means the source project updated it since it was last downloaded.
3. For each selected entry: `deep knowledge pull <kind> --project <org>/<project> --headless`.
4. Read the `file` path from each JSON output with the Read tool and use the content.
5. Errors: `Api` 403 → the user must be a member of the source project; suggest asking a project manager to add them. `Api` 404 `KNOWLEDGE_NOT_FOUND` → that kind doesn't exist; show the catalog instead.

## Push flow (publishing this project's knowledge)

1. Decide the kind. Check `deep knowledge list --headless` first — update the existing kind rather than creating a near-duplicate.
2. Compose the FINAL markdown — it is a full replacement, not a diff. If a version already exists, pull it first and merge rather than dropping content silently.
3. Write it to `.deep/knowledge/<kind>.md` with the Write tool.
4. Run once: `deep knowledge push <kind> --description "<1-2 sentences: what this doc covers>" --headless` (add `--title "..."` on first publish).
5. Interpret the JSON:
   - `ok: true` → report kind + `version` and whether it was `created` or updated.
   - `Api` 409 `KNOWLEDGE_VERSION_CONFLICT` → someone updated it meanwhile; pull, merge, push again.
   - `Api` 413 `KNOWLEDGE_CONTENT_TOO_LARGE` → trim the document below 1 MiB.
   - `NotAuthenticated` / `NoProjectBinding` → usual `deep login` / `deep init` guidance.

The `--description` text is what OTHER agents see in the catalog when deciding whether to download — keep it accurate and update it whenever the content's scope changes.

## Writing rules — the reader is another project's coding agent

The audience is NOT a human and can NOT see this repository's code. Produce the document accordingly:

- Self-contained: no "see the code", "as described above in our module" references.
- Contract-first and concrete: endpoints with full path + method + request/response JSON shapes + error codes; DB schemas with table/column/type/constraints; events with payload schemas. Include example requests/responses.
- No narrative or marketing language. Machine-scannable structure: headings, tables, code blocks. No vagueness — exact values instead of "usually" / "something like".
- Mark breaking changes and deprecated fields explicitly.
- Fixed opening convention:
  ```
  # <Title>
  > Audience: coding agents of other projects. Source: <org>/<project>, kind: <kind>.
  ```
