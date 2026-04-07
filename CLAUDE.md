# Wiki Instructions

## Directory Structure

- `raw/` — all source documents land here (articles, papers, PDFs, clippings). Never modify files here.
- `daily/` — one .md per day for fleeting notes
- `wiki/` — compiled knowledge base, organized by topic subdirectory
- `resources/queue/` — things to read/watch
- `resources/archive/` — consumed resources
- `projects/` — one page per active project
- `archive/` — completed or dormant material
- `CLAUDE.md` — these instructions (auto-loaded by Claude Code)
- `HUMAN.md` — suggestions from Claude to the owner

## Constraints
- Never modify files in `raw/`
- Never delete wiki pages — mark as merged or deprecated instead
- Do not create pages for concepts mentioned only once
- Do not create a new topic directory unless at least 3 pages exist or are expected

## Wiki Structure

- `wiki/` is organized into topic subdirectories (e.g. `wiki/ai/`, `wiki/physics/`)
- Each topic directory has its own `index.md` listing all pages in that topic
- The root `wiki/index.md` lists all topics with a one-line description each
- Place a concept in the most specific relevant topic
- If a concept spans multiple topics, place it in the dominant one and cross-link from others

## Wiki Page Format

---
title:
topic:
created: YYYY-MM-DD
updated: YYYY-MM-DD
---

# Page Title

> One-to-three sentence summary.

## Content

Main body. Use sections as needed.

## See Also

- [[topic/related-page]]

## Backlinks

<!-- AUTO: pages that link to this one -->

## Sources

- raw/original-document.md

## Conventions

### File Naming
- lowercase kebab-case: `distributed-systems.md`, `bayes-theorem.md`
- one concept per page, prefer narrow and specific over broad

### Cross-Linking
- use full paths in wikilinks: `[[ai/transformers]]` not `[[transformers]]`
- link generously but not repeatedly within the same paragraph
- when adding a link to page A pointing to page B, add A to B's backlinks

### Summaries
- every page opens with a blockquote summary after the title
- write summaries last, after content is complete

## Resource Queue Format

Files in `resources/queue/` use this frontmatter:

---
title:
type: book | article | paper | video | podcast
author:
year:
format: pdf | url | epub
status: queued | in-progress | done
url:
---

## Workflow

1. **Extract**: read source in `raw/`, identify distinct concepts worth capturing
2. **Assign**: determine which topic directory the concept belongs to
3. **Write**: create or update wiki page in `wiki/[topic]/`
4. **Link**: add `[[cross-links]]` between related pages
5. **Backlink**: update backlinks on all referenced pages
6. **Summarize**: write or revise the blockquote summary
7. **Index**: update topic `index.md` and root `wiki/index.md`
8. **Suggest**: append any observations or gaps to `HUMAN.md`
9. **Commit & push**: stage all changes, commit with a short descriptive message, and push to the remote

## Principles
- atomic pages — one idea per page, linked to related ideas
- write for your future self, assume no recent context
- never copy-paste from raw/, always rewrite in your own words
- when two pages overlap significantly, consolidate and redirect