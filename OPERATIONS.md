# Wiki Operations

Protocols for the five operations Claude runs against this vault. See `CLAUDE.md` for schema, `TEMPLATES.md` for page templates, `FORMATS.md` for `index.md` and `log.md` layouts.

## INGEST (autonomous)

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

## INGEST --interactive

Trigger: user says `ingest raw/<path> --interactive`

Steps:
1. Read the source
2. Present: "Here are the key takeaways I'm seeing: [list]. Anything you want me to emphasize or skip?"
3. Wait for user input
4. Proceed with autonomous ingest steps, applying the guidance given
5. Show the draft `wiki/sources/<slug>.md` summary: "Here's the summary — does this look right before I file it?"
6. After approval, complete steps 3–7 from autonomous ingest

## INGEST BATCH

Trigger: user says `ingest batch raw/<dir>` or provides a list of files to ingest together

Use when ingesting many files at once (5+). Groups related files into topically-coherent batches, writes a plan to `TODO.md`, then processes one batch at a time with user confirmation between batches.

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

## QUERY

Trigger: user says `query: <question>` or asks a research question naturally

Steps:
1. Enumerate `index.md` entries (grep + sliced reads — see `FORMATS.md`) to identify relevant pages
2. Read the relevant concept/entity pages
3. Read relevant source pages if more detail is needed
4. Write a synthesized answer with inline `[[wikilink]]` citations
5. Ask: "Worth filing this as a synthesis page?" — if yes, create `wiki/synthesis/<slug>.md`, update `index.md` and `log.md`

## LINT

Trigger: user says `lint`

Check and report:
1. **Orphans:** Pages in `wiki/` with no inbound wikilinks from any other wiki page
2. **Stubs ready to flesh out:** Pages with `status: stub` that have 2+ sources referencing them
3. **Missing pages:** Wikilinks pointing to files that don't exist
4. **Stale claims:** Pages making claims contradicted by more recently ingested sources
5. **Tag drift:** Pages missing tags, or using tags outside the approved vocabulary
6. **Source count drift:** Pages where `sources:` frontmatter count doesn't match actual `[[wiki/sources/...]]` links in `## Sources`

Present findings as a checklist. Ask which items to fix now.
