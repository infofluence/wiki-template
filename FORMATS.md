# Wiki File Formats

Layouts for the two index/log files Claude maintains. Page templates live in `TEMPLATES.md`.

## index.md

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

## log.md

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
