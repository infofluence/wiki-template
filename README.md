# Obsidian Wiki Template (Claude/Codex driven)

A template for a personal research wiki in [Obsidian](https://obsidian.md), maintained by Claude Code or Codex. You drop raw sources (articles, papers, transcripts) into `raw/`. Claude reads them and builds a structured wiki of concepts, entities, sources, and synthesis pages in `wiki/`. 
The idea was published by Andrej Karpathy at https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## How it works

- **You** curate `raw/`: clip articles, drop PDFs, save transcripts. This folder is read-only to Claude.
- **Claude** owns `wiki/`: on `ingest raw/<path>`, it reads the source, creates a source summary, extracts concepts and entities into linked pages, and maintains an index.
- **Obsidian** renders the result as a navigable knowledge graph via wikilinks.

## Setup

1. **Clone this repo** as the root of a new Obsidian vault, or copy these files into an existing one.
2. **Customize `CLAUDE.md`:**
   - Replace `[TOPIC]` in the Purpose section with your wiki's domain.
   - Replace the placeholder tag vocabulary with your controlled tag list.
3. **Open the folder in Obsidian** (File → Open vault → this directory).
4. **Open the folder in Claude Code** (`claude` from this directory). On first run Claude reads `CLAUDE.md`, `log.md`, and `index.md` and reports vault status.
5. **Optional:** install the [Obsidian Web Clipper](https://obsidian.md/clipper) and configure it to save into `raw/articles/`.

## Usage

Inside Claude Code:

| Command | What it does |
|---------|-------------|
| `ingest raw/articles/foo.md` | Read source, create wiki pages, update index |
| `ingest raw/papers/bar.pdf --interactive` | Same, with confirmation steps |
| `ingest batch raw/articles/` | Plan and run a multi-file ingest |
| `query: <question>` | Synthesize an answer from existing wiki pages |
| `lint` | Health check: orphans, missing pages, stale claims, tag drift |

Full protocols live in `OPERATIONS.md`.

## Repository layout

```
raw/           # your sources — Claude reads, never writes
wiki/          # Claude-maintained pages (concepts, entities, sources, synthesis)
CLAUDE.md      # schema, naming, linking, tag rules
OPERATIONS.md  # ingest / query / lint protocols
TEMPLATES.md   # page templates
FORMATS.md     # index.md and log.md layouts
index.md       # catalog of every page in the wiki
log.md         # append-only operation history
```

## Requirements

- [Claude Code](https://claude.com/claude-code) CLI by Anthropic or 
- [Codex](https://developers.openai.com/codex/cli) CLI by Open AI
- [Obsidian](https://obsidian.md) (for viewing — Claude operates on the files directly, so Obsidian isn't required to ingest)
