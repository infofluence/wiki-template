# LLM Wiki — Schema & Rules

This file governs all wiki operations in this Obsidian vault.

Companion files:
- `OPERATIONS.md` — protocols for INGEST, QUERY, LINT (read when running an operation)
- `TEMPLATES.md` — page templates for concepts, entities, sources, synthesis
- `FORMATS.md` — layouts for `index.md` and `log.md`

## Purpose

<!-- TODO: replace this paragraph with the topic/domain this wiki covers and any source-type notes. -->
This is a personal research wiki on **[TOPIC]**. Source types include [e.g. web articles, academic papers, YouTube transcripts, misc documents].

**Your role:** You own the `wiki/` layer entirely. You create, update, and maintain all pages. The human curates raw sources, directs analysis, and asks questions.

**Invariant:** `raw/` is read-only. Never create, modify, or delete files in `raw/`.

## Directory Structure

```
wiki-vault/
├── raw/
│   ├── articles/        # web clips (markdown from Obsidian Web Clipper)
│   ├── papers/          # PDFs, academic papers
│   ├── transcripts/     # YouTube extracts
│   ├── other/           # misc sources
│   └── assets/          # locally downloaded images
├── wiki/
│   ├── concepts/        # ideas, techniques, patterns
│   ├── entities/        # named tools, models, people, orgs
│   ├── sources/         # one summary page per ingested source
│   └── synthesis/       # comparisons, analyses, filed query answers
├── CLAUDE.md            # this file
├── OPERATIONS.md        # operation protocols
├── TEMPLATES.md         # page templates
├── FORMATS.md           # index.md and log.md layouts
├── index.md             # content catalog
└── log.md               # append-only operation log
```

## Page Types

- **concepts/** — Ideas, techniques, patterns
- **entities/** — Named things: tools, models, people, orgs
- **sources/** — One page per raw source. Never modify after creation except to add cross-references.
- **synthesis/** — Filed answers to queries, comparisons, analyses.

**Entity scope:** Create entity pages for recurring researchers, products, or organizations (≥2 sources, or clear domain importance). Do **not** create entity pages for one-time article authors — note them in the source page's `Notes` section instead.

## Naming Conventions

| Type | Convention | Example |
|------|-----------|---------|
| concepts | full descriptive slug | `retrieval-augmented-generation.md` |
| entities | canonical short name | `ollama.md`, `langchain.md` |
| sources (paper) | `<title-slug>-<author>-<year>.md` | `attention-is-all-you-need-vaswani-2017.md` |
| sources (article/transcript) | `<title-slug>.md` | `practical-guide-to-rag.md` |
| synthesis | descriptive outcome | `rag-vs-finetuning-comparison.md` |

All names: lowercase, hyphen-separated, no spaces.

**Date fields:**
- Source pages — `created:` = original publish date of the source if known, otherwise today; `date_ingested:` = today.
- Concept/entity pages — `created:` = first-ingest date; `updated:` = today on every modification.

## Linking Rules

1. Use full-path wikilinks: `[[wiki/entities/ollama]]` (not `[[ollama]]`)
2. Link every new concept or entity on **first mention** in a page, even if the target page doesn't exist yet
3. Source pages link to all concepts/entities they cover
4. Concept/entity pages do NOT back-link to sources — use `sources:` frontmatter count instead
5. The `## Related` section uses wikilinks; the `## Sources` section uses wikilinks to `wiki/sources/`

## Tag Vocabulary

<!-- TODO: replace with the controlled tag list for this wiki's topic. Keep it short (5–15 tags). -->
Use only these tags (add multiple when relevant):

`tag-1`, `tag-2`, `tag-3`

<!-- Optional rule: e.g. "For YouTube-sourced content, always include `youtube`." -->

## Operations

Triggers — see `OPERATIONS.md` for full protocols. Read it when an operation runs.

| Trigger | Operation |
|---------|-----------|
| `ingest raw/<path>` | INGEST (autonomous) |
| `ingest raw/<path> --interactive` | INGEST with confirmation steps |
| `ingest batch raw/<dir>` | INGEST BATCH (multi-file, planned) |
| `query: <question>` or a research question | QUERY |
| `lint` | LINT (health check) |

## Session Start Protocol

At the start of every session:
1. Read this file (CLAUDE.md)
2. Read `log.md` — last 5 entries to understand recent activity
3. Read `index.md` — to know the current state of the wiki
4. Confirm ready: "Wiki loaded. Index has N concepts, M entities, P sources. Last activity: [date/operation]. What would you like to work on?"
