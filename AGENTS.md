# Obsidian Vault Rules

## Vault Structure (PARA)

This vault follows the PARA method:

| Folder | Purpose | Contains |
|--------|---------|----------|
| `0. Common/` | Shared files | Daily notes, weekly notes, dashboard, KB index, log |
| `1. Projects/` | Active projects | Time-bound work with deliverables |
| `2. Areas/` | Ongoing areas | Responsibilities with no end date |
| `3. Resources/` | Reference material | Topic-organized resources |
| `4. Archive/` | Inactive items | Completed or paused projects |
| `Inbox/` | Raw input | New documents awaiting classification |

## Knowledge Base Rules

This vault uses the LLM Knowledge Base pattern (inspired by Karpathy's llm-wiki):
- **Inbox/** = raw input (landing zone)
- **PARA directories** = compiled knowledge (organized, linked, indexed)
- **AGENTS.md** = schema (rules governing the vault)

### Operations

| Operation | Command | When |
|-----------|---------|------|
| **Ingest** | `/kb-ingest` | New documents arrive in Inbox |
| **Query** | `/kb-query "question"` | Search and synthesize from vault |
| **Lint** | `/kb-lint` | Periodic health check |
| **Index** | `/kb-index` | Rebuild all indexes after reorganization |

If Obsidian CLI is unavailable, use filesystem fallbacks for search, tags, and link inspection.

### Inbox Structure Notes

- `Inbox/**/README.md` files are structural notes that explain the intended use of local folders.
- Keep these README files in place unless the user explicitly requests otherwise.
- Exclude these README files from ingest, rename, move, summary generation, backlink updates, and routine KB search results unless the task is specifically about folder structure or inbox organization.

### Index System

- `0. Common/index.md` — top-level vault overview (~30 lines)
- `{category}/_index.md` — per-category index (~10-20 lines each)
- `0. Common/log.md` — append-only activity log

**Index update rules:**
- Update relevant `_index.md` when adding, moving, or archiving documents
- Update `0. Common/index.md` when category-level changes occur
- Append to `log.md` for every ingest, lint, or index-rebuild operation

### Tag Conventions

No existing nested tag convention was detected during initialization, so the default prefixes below are established for this vault.

| Prefix | Purpose | Example |
|--------|---------|---------|
| `#proj/<name>` | Project identifier | `#proj/my-project` |
| `#type/<type>` | Document type | `#type/paper`, `#type/meeting`, `#type/idea` |
| `#topic/<topic>` | Subject matter | `#topic/kubernetes`, `#topic/ml` |

Tags enable cross-folder collection. A paper in `2. Areas/Paper/` tagged `#proj/my-project` can be found when querying the project.

Optional 4th prefix (if the vault adopts research-paper tagging later): `#study/paper/<topic>`.

### Wikilink Rules

- Use `[[wikilinks]]` for all internal vault references
- Obsidian tracks renames automatically for wikilinks
- Use `[text](url)` only for external URLs
- When ingesting, auto-link the first occurrence of known document titles
- Keep auto-links selective to avoid noisy notes

### Frontmatter Standard

Every document in PARA directories should have:
```yaml
---
title: Document Title
created: YYYY-MM-DD
type: paper|note|reference|meeting|idea|log
tags: []
---
```

Optional fields: `related`, `source`, `summary`

## Safety Rules

- Do not modify `.obsidian/` settings unless explicitly requested.
- Treat `Templates/` and `.claude/skills/` as source scaffolding; do not repurpose generated KB files as templates.
- Keep generated KB maintenance files in English unless a note explicitly requires another language.
- Do not ingest, move, rename, or rewrite `Inbox/**/README.md` files unless the user explicitly asks for that action.