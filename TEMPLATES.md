# Wiki Page Templates

Reference for CLAUDE.md. Used when creating new wiki pages.

## concepts/

Ideas, techniques, and patterns.

Template:
```markdown
---
type: concept
tags: [tag-1, tag-2, ...]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 0
status: stub
---

# Concept Name

One-sentence definition.

## Overview

[2-3 paragraph explanation]

## Key Properties

- Property 1
- Property 2

## Trade-offs

| Pro | Con |
|-----|-----|
| ... | ... |

## Related

- [[wiki/concepts/related-concept]]
- [[wiki/entities/relevant-tool]]

## Sources

- [[wiki/sources/source-slug]]
```

## entities/

Named things: tools, models, people, organizations.

Template:
```markdown
---
type: entity
tags: [tag-1, tag-2, ...]
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: 0
status: stub
---

# Entity Name

One-sentence description (what it is and why it matters).

## Overview

[2-3 paragraphs]

## Key Features / Characteristics

- Feature 1
- Feature 2

## Context in This Research

[How this entity relates to the research domain]

## Related

- [[wiki/concepts/related-concept]]
- [[wiki/entities/related-entity]]

## Sources

- [[wiki/sources/source-slug]]
```

## sources/

One page per ingested raw source. Never modify after creation except to add cross-reference links.

Template:
```markdown
---
type: source
source_type: article | paper | transcript | other
title: "Full original title"
url: "https://..."
date_ingested: YYYY-MM-DD
tags: [...]
---

# Source Title

**Type:** article / paper / transcript / other
**Date ingested:** YYYY-MM-DD

## Summary

[3-5 paragraph summary of key content]

## Key Claims

- Claim 1
- Claim 2

## Entities Mentioned

- [[wiki/entities/entity-slug]]

## Concepts Covered

- [[wiki/concepts/concept-slug]]

## Quotes / Extracts

> Notable quote or excerpt

## Notes

[Anything notable about reliability, recency, author credibility]
```

## synthesis/

Filed answers to queries, comparisons, analyses. Created when a query answer is worth keeping.

Template:
```markdown
---
type: synthesis
tags: [...]
created: YYYY-MM-DD
sources: [source-slug-a, source-slug-b]
---

# Title

**Question:** What prompted this synthesis?

## Answer / Analysis

[Main content]

## Evidence

- Point from [[wiki/sources/source-a]]
- Point from [[wiki/sources/source-b]]

## Caveats / Open Questions

- ...
```
