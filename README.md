# PARA Knowledge Base for Obsidian

> **LLM as Knowledge Compiler**
> A knowledge-base template and skill set that adapts Karpathy's llm-wiki pattern for a PARA-based Obsidian vault.

---

## Overview

This repository is a scaffold for operating an Obsidian vault not as a searchable pile of files, but as a knowledge base that an LLM can continuously organize and maintain.

If you only keep dumping notes into a vault, an agent has to reread large parts of the vault, rediscover the structure, and summarize the same material again and again.
This project avoids that by providing the following pieces.

- Knowledge-base operating rules for PARA
- Vault schema defined in `AGENTS.md`
- Hierarchical indexes via `_index.md` and `0. Common/index.md`
- `kb-*` skills for ingest, query, lint, and reindexing
- Supporting skills for Obsidian CLI, Markdown, Bases, and Canvas workflows

This repository is less a plugin distribution and more a scaffold for initializing and operating an Obsidian vault.

---

## Background

The core idea is inspired by Karpathy's llm-wiki.
The important shift is to use an LLM not as a search engine that repeatedly scans raw data, but as a maintainer of compiled knowledge that can be updated over time.

An Obsidian personal vault is closer to a gradually evolving knowledge base than to a massive document corpus, which makes this approach a good fit.

---

## Why This Exists

Even if your vault is reasonably organized with PARA, an agent still pays the following cost on each conversation.

**Without a knowledge base:**
```text
User: "What do you know about distributed systems?"
Agent: Reads 30+ related notes -> summarizes them -> session ends and the context disappears
Next week the user asks again
Agent: Reads the same 30+ notes again
```

**With this project:**
```text
/kb-ingest
  -> classify, move, link, and index new notes

/kb-query "distributed systems"
  -> read indexes first and narrow down the target notes
  -> answer with much less reading
```

### What This Adds to an Existing Vault

| What you already have | What this project adds |
|---|---|
| PARA-based folder organization | Per-category `_index.md` files and `0. Common/index.md` |
| Fragmented tags | Tag convention detection and consistency checks |
| Manual wikilinks | Automatic wikilink generation during ingest |
| Scattered operating rules | A vault schema centralized in `AGENTS.md` |
| Unsorted notes piling up in Inbox | Classification, moving, and metadata assignment via `/kb-ingest` |
| Know-how for isolated manual operations | Obsidian CLI / Markdown / Bases / Canvas skills |

### Example of Better Token Efficiency

| Task | Without a knowledge base | With this project |
|---|---|---|
| "What projects are currently active?" | Broadly reread `Projects/` | Narrow from `0. Common/index.md` |
| "Collect what I know about topic X" | Explore the whole vault like grep | Use indexes, tags, and backlinks to limit scope |
| Reflect a new document | Manually move, tag, link, and update indexes | Run the whole flow with `/kb-ingest` |
| Periodic maintenance | Check manually | Detect orphaned docs and broken links with `/kb-lint` |

What matters is that indexes are cheap to read and make the next place to look explicit.

---

## Concept

### The Three Layers

```text
Inbox/                          <- raw input
0. Common/ + 1-4 PARA folders   <- organized and indexed knowledge
AGENTS.md                       <- structure and operating rules for the vault
```

- **Inbox/** is the landing zone before ingest.
- **0. Common/** and the **PARA directories** hold the organized knowledge base itself.
- **AGENTS.md** acts as the schema for the agent. It defines the folder structure, tag usage, and how indexes should be maintained.

### Three Core Operations

| Operation | Role |
|---|---|
| **Ingest** | Classify new notes and integrate them into the right place |
| **Query** | Read compiled knowledge and answer questions |
| **Lint** | Detect structural drift and maintenance gaps |

### How This Differs from RAG

| RAG | Knowledge Base |
|---|---|
| Retrieves fragments again for each query | Compiles knowledge once and updates it incrementally |
| Quality depends heavily on retrieval at query time | Structure is established during ingest, so results stay more consistent |
| Summaries tend to disappear with the session | Summaries and links remain in the vault |
| Better for very large corpora | Better for personal knowledge management and continuous upkeep |

---

## Main Skills Included

### `/kb-init`
Initializes the Knowledge Base layer.

- Create `_index.md` files for each category
- Create `0. Common/index.md`
- Create `0. Common/log.md`
- Append to an existing `AGENTS.md` or create one from a template
- Check whether Obsidian CLI is available

### `/kb-ingest`
Ingest documents from Inbox or a specified path.

- PARA classification
- Moving files
- Adding frontmatter
- Generating wikilinks
- Updating related documents
- Updating indexes and log

### `/kb-query`
Searches, summarizes, and compares against the Knowledge Base structure.

- Index-driven exploration
- Cross-tag search
- Backlink traversal
- Multi-document synthesis when needed

### `/kb-lint`
Checks the health of the Knowledge Base.

- index drift
- orphaned documents
- tag inconsistencies
- broken wikilinks
- missing frontmatter
- stale content

### `/kb-index`
Updates indexes.

- diff-based smart update
- full rebuild with `--full`
- category-level updates
- dry-run verification

---

## Bundled Supporting Skills

This repository includes not only Knowledge Base-specific skills, but also general-purpose skills that support Obsidian work.

- `obsidian-cli`
- `obsidian-markdown`
- `obsidian-bases`
- `json-canvas`
- `defuddle`
- `write-a-skill`

That lets you handle Knowledge Base operations and day-to-day Obsidian editing, search, and structuring from the same working foundation.

---

## Repository Structure

| Path | Role |
|---|---|
| `.claude/skills/` | Knowledge Base and Obsidian-related skills |
| `Templates/_index.md.template` | Template for category indexes |
| `Templates/AGENTS.md.template` | Template for vault rules |
| `.obsidian/` | Obsidian vault settings |
| `Welcome.md` | Initial note |

---

## Setup

### How to Use It

This repository assumes one of the following usage patterns.

1. **Use it as a template for a new Obsidian vault**
2. **Import the necessary files into an existing Obsidian vault**

At minimum, the vault root should contain `.obsidian/`, and your agent should be able to open the vault root as its working directory.

### Expected PARA Layout

This project assumes a PARA layout like the following.

```text
Vault/
├── 0. Common/
├── 1. Projects/
├── 2. Areas/
├── 3. Resources/
├── 4. Archive/
└── Inbox/
```

`kb-init` can detect missing pieces of the layout and help initialize them when needed.

### Main Purpose of Each Folder

In this vault, each folder plays the following role.

| Folder | Main purpose | Typical contents |
|---|---|---|
| `0. Common/` | Shared operational files for the entire vault | `index.md`, `log.md`, dashboards |
| `1. Projects/` | Active work with deadlines or deliverables | project notes, plans, tasks, meeting notes |
| `2. Areas/` | Ongoing responsibility areas without an end date | career, health, family, recurring work management notes |
| `3. Resources/` | Reference knowledge and materials | technical notes, research notes, cheat sheets, reading notes |
| `4. Archive/` | Storage for completed or inactive items | finished projects, old references, past logs |

As a rule of thumb:

- Things with an end date and a completion goal belong in `1. Projects/`
- Ongoing responsibilities you maintain over time belong in `2. Areas/`
- Knowledge and materials kept for future reference belong in `3. Resources/`
- Items that are no longer active belong in `4. Archive/`
- Shared vault-wide operational files belong in `0. Common/`

`0. Common/` is an additional shared operations layer on top of the standard four PARA categories.
It is therefore recommended for files shared across the whole vault, not as a catch-all location for topic notes.

### Example Folder Layout After Initialization

After running `kb-init`, the vault will typically look something like this.

```text
Vault/
├── .obsidian/
├── AGENTS.md
├── Inbox/
│   ├── clipped-article.md
│   └── meeting-note.md
├── 0. Common/
│   ├── index.md
│   ├── log.md
│   ├── daily/
│   └── weekly/
├── 1. Projects/
│   ├── _index.md
│   ├── Project Alpha/
│   │   ├── overview.md
│   │   ├── tasks.md
│   │   └── meeting-2026-05-04.md
│   └── Project Beta/
│       └── overview.md
├── 2. Areas/
│   ├── _index.md
│   ├── Paper/
│   │   ├── survey-llm.md
│   │   └── rl-note.md
│   └── Career/
│       └── resume-notes.md
├── 3. Resources/
│   ├── _index.md
│   ├── Infra/
│   │   └── kubernetes-cheatsheet.md
│   └── MLOps/
│       └── feature-store.md
└── 4. Archive/
  ├── _index.md
  └── Project Alpha/
    └── retrospective.md
```

The key points are:

- `AGENTS.md` is placed at the root to define vault rules.
- `0. Common/` contains the global `index.md` and operation log `log.md`.
- `1. Projects/` through `4. Archive/` each get a category-level `_index.md`.
- `Inbox/` is where unorganized notes arrive before `/kb-ingest` routes them to the proper category.

---

## Quick Start

1. Prepare an Obsidian vault using this repository structure
2. Open the vault root as the agent's working directory
3. Run `/kb-init`
4. Put new notes into `Inbox/`
5. Run `/kb-ingest`
6. Ask `/kb-query "What do I know about X?"`
7. Run `/kb-lint` periodically to check maintenance health
8. After major reorganizations, run `/kb-index`

---

## Obsidian CLI Integration

If Obsidian CLI is available, search, backlink resolution, and tag operations become more precise.
It is not required, but it makes the workflow noticeably more stable.

### What You Can Do

| CLI command | Main use |
|---|---|
| `obsidian search query="term"` | Full-text search |
| `obsidian backlinks file="Note"` | Backlink exploration |
| `obsidian tags sort=count counts` | Understand tag conventions |
| `obsidian property:set ...` | Update frontmatter |
| `obsidian read ...` | Retrieve note contents |

### If CLI Is Not Available

The skills still work by falling back to file-based exploration.
That remains usable, but search and backlink resolution will be somewhat less accurate and less efficient.

---

## Requirements

- Obsidian vault
- An agent runtime that can use the skills in this repository
- Recommended: a PARA-style folder structure
- Optional: Obsidian 1.12+ and Obsidian CLI

---

## Who This Is For

- You have many notes in Obsidian, but do not want AI to rescan everything every time
- You already organize with PARA and want explicit indexes and rules
- You want a consistent flow for bringing material in from Inbox
- You want a maintained knowledge base, not just one-off search

---

## Notes

The core of this project is not just to make search faster, but to keep a maintainable knowledge structure inside the vault that an LLM can work with over time.
That is why it emphasizes indexes, wikilinks, frontmatter, and operating rules rather than only full-text search.