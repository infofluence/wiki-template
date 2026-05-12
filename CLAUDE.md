# LLM Wiki — Schema & Rules

This file governs all wiki operations in this Obsidian vault.

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
├── TEMPLATES.md         # page templates (concepts, entities, sources, synthesis)
├── index.md             # content catalog
└── log.md               # append-only operation log
```

## Page Types

Templates: see `TEMPLATES.md`.

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

### INGEST (autonomous)

Trigger: user says `ingest raw/<path>`

Steps:
1. Read the source file from `raw/`

   **PDF sources (`raw/papers/`):** Read in 20-page batches (`pages: "1-20"`, then `"21-40"`, etc.). Determine total length from the table of contents or by reading until content is exhausted. If a page returns blank text (section-opener image, etc.), note the gap — the user may supply screenshots to fill it. Proceed with steps 2–7 once full content is captured.

2. Compute the source slug. If `wiki/sources/<slug>.md` already exists, **stop and report** — do not overwrite. Otherwise create it from the source template.
3. For every concept mentioned: create or update `wiki/concepts/<slug>.md`
4. For every entity mentioned: create or update `wiki/entities/<slug>.md`
   - If updating: add to the relevant section, increment `sources:` frontmatter, set `updated:` to today
   - If creating: use the template, set `status: stub`
5. Add a link to the new source page in the `## Sources` section of every concept/entity page it touches
6. Update `index.md`: add the source to `## Sources`, update any modified concept/entity entries
7. Append to `log.md`

**Scope guideline:** A single source typically touches 3-8 concept/entity pages. If touching more than 15, check that you're making focused updates not wholesale rewrites.

### INGEST --interactive

Trigger: user says `ingest raw/<path> --interactive`

Steps:
1. Read the source
2. Present: "Here are the key takeaways I'm seeing: [list]. Anything you want me to emphasize or skip?"
3. Wait for user input
4. Proceed with autonomous ingest steps, applying the guidance given
5. Show the draft `wiki/sources/<slug>.md` summary: "Here's the summary — does this look right before I file it?"
6. After approval, complete steps 3–7 from autonomous ingest

### INGEST BATCH

Trigger: user says `ingest batch raw/<dir>` or provides a list of files to ingest together

Use when ingesting many files at once (5+). Groups related files into topically-coherent batches, writes a plan to `TODO.md`, then processes one batch at a time with user confirmation between batches.

Steps:

**Phase 1 — Plan (do once at the start)**
1. Read all source files in parallel to understand their content
2. Group files into 4–7 topically-coherent batches; assign priority (HIGH / MEDIUM / LOW) based on relevance to core wiki themes
3. Write the batch plan to `TODO.md`:
   - One `## Batch N — <Topic> ⬛ PRIORITY` section per batch with a one-line description
   - One `- [ ] ingest raw/<path>` checkbox per file
4. Report the plan to the user: batch names, file counts, priority levels. Ask for confirmation before proceeding.

**Phase 2 — Execute (repeat per batch)**
1. Read all files in the current batch in parallel
2. Scope the batch: determine which concepts/entities to create vs. update before writing any pages
3. For each file in the batch: execute the full autonomous INGEST protocol (steps 1–7), then mark its checkbox `[x]` in `TODO.md`
4. After the last file in the batch: report pages created, pages updated, and source count delta
5. Pause and ask the user to confirm before proceeding to the next batch

**Rules**
- `TODO.md` is temporary working state — it tracks in-progress multi-session batch ingests. Clean it up (archive or delete) once all batches are complete.
- Read all files in a batch before writing any pages — scoping first prevents redundant concept pages.
- If a session ends mid-batch, `TODO.md` checkboxes show exactly where to resume next session.

### QUERY

Trigger: user says `query: <question>` or asks a research question naturally

Steps:
1. Enumerate `index.md` entries (grep + sliced reads — see "Reading `index.md`" below) to identify relevant pages
2. Read the relevant concept/entity pages
3. Read relevant source pages if more detail is needed
4. Write a synthesized answer with inline `[[wikilink]]` citations
5. Ask: "Worth filing this as a synthesis page?" — if yes, create `wiki/synthesis/<slug>.md`, update `index.md` and `log.md`

### LINT

Trigger: user says `lint`

Check and report:
1. **Orphans:** Pages in `wiki/` with no inbound wikilinks from any other wiki page
2. **Stubs ready to flesh out:** Pages with `status: stub` that have 2+ sources referencing them
3. **Missing pages:** Wikilinks pointing to files that don't exist
4. **Stale claims:** Pages making claims contradicted by more recently ingested sources
5. **Tag drift:** Pages missing tags, or using tags outside the approved vocabulary
6. **Source count drift:** Pages where `sources:` frontmatter count doesn't match actual `[[wiki/sources/...]]` links in `## Sources`

Present findings as a checklist. Ask which items to fix now.

## index.md Format

```markdown
# Wiki Index

_Last updated: YYYY-MM-DD — N sources ingested_

## Concepts
- [[wiki/concepts/concept-slug]] — One-line description (sources: N)

## Entities
- [[wiki/entities/entity-slug]] — One-line description (sources: N)                    # active
- [[wiki/entities/entity-slug]] — One-line description (sources: N, status: stub)      # stub

## Sources
- [[wiki/sources/source-slug]] — Original title (type, YYYY-MM-DD)

## Synthesis
- [[wiki/synthesis/synthesis-slug]] — One-line description (YYYY-MM-DD)
```

One-line descriptions: aim for ≤ 120 characters.

Sections are kept in alphabetical order. Insert new entries in their approximate alphabetical position; do **not** reshuffle existing entries that have drifted out of order — leave them in place to keep diffs small. Update the "Last updated" line on every change.

**Reading `index.md`:** the file may exceed the Read tool's token limit at scale. Use `grep -n "^## \|^- \[\[wiki/<section>/" index.md` to enumerate entries, then `Read` with `offset`/`limit` to fetch slices. Do not attempt to read the whole file in one call once it grows large.

## log.md Format

Append-only. Each entry header starts with `## [YYYY-MM-DD]` for grep-parseability.

```markdown
## [YYYY-MM-DD] ingest | Source Title
- Source: raw/articles/filename.md
- Pages created: sources/slug, concepts/slug-1, entities/slug-2
- Pages updated: concepts/existing-concept, index.md
- Notes: (optional)

## [YYYY-MM-DD] query | Question text
- Pages consulted: concepts/slug, entities/slug
- Filed as: synthesis/slug (or "not filed")

## [YYYY-MM-DD] lint | health check
- Orphans found: N — list
- Stubs ready: list
- Missing pages: list
- Action taken: (what was fixed)
```

## Session Start Protocol

At the start of every session:
1. Read this file (CLAUDE.md)
2. Read `log.md` — last 5 entries to understand recent activity
3. Read `index.md` — to know the current state of the wiki
4. Confirm ready: "Wiki loaded. Index has N concepts, M entities, P sources. Last activity: [date/operation]. What would you like to work on?"
