# Personal Wiki System

## Directory Structure

- `raw/` — Source documents (PDFs, articles, notes, clippings, transcripts). Unprocessed input.
- `wiki/` — Compiled markdown knowledge base. Every file here is a standalone wiki page.

## Wiki Page Format

Each file in `wiki/` follows this structure:

```markdown
# Page Title

> One-to-three sentence summary of the topic.

## Content

Main body. Use sections as needed.

## See Also

- [[related-page]]
- [[another-page]]

## Backlinks

<!-- AUTO: pages that link to this one -->
- [[linking-page]]

## Sources

- raw/original-document.pdf
- raw/notes-2024-03-15.md
```

## Conventions

### When to Create a New Page
- A concept appears in more than one source
- A concept has more than ~200 words of content
- Otherwise, cover it inline in a related page

### File Naming

- Use lowercase kebab-case: `distributed-systems.md`, `bayes-theorem.md`.
- One concept per page. Prefer narrow, specific pages over broad ones.

### Cross-Linking

- Use `[[page-name]]` syntax (without `.md` extension) for internal links.
- Link generously — any mention of a concept that has its own page should be linked on first use within a section.
- Do not link the same term repeatedly within the same paragraph.

### Summaries

- Every wiki page must open with a blockquote summary immediately after the title.
- Summaries should be self-contained — understandable without reading the full page.
- Write summaries last, after the content is complete.

### Backlinks

- The `## Backlinks` section lists all pages that link *to* this page.
- When adding a `[[link]]` to page A that points to page B, also add page A to page B's backlinks section.
- Keep backlinks sorted alphabetically.

### Sources

- The `## Sources` section traces content back to files in `raw/`.
- Use relative paths from the repo root: `raw/filename.ext`.
- A wiki page may synthesize multiple sources.

## Workflow

1. **Ingest**: Place source material in `raw/`.
2. **Extract**: Read a source and identify distinct concepts worth capturing.
3. **Write**: Create or update a wiki page in `wiki/` for each concept.
4. **Link**: Add `[[cross-links]]` between related pages.
5. **Backlink**: Update backlinks on all pages referenced by new or changed pages.
6. **Summarize**: Write or revise the blockquote summary.

## Principles

- Prefer atomic pages — one idea per page, linked to related ideas.
- Write for your future self. Assume no recent context.
- Content in `wiki/` should never be a raw copy-paste from `raw/`. Always rewrite in your own words.
- When two pages overlap significantly, consolidate into one and redirect the other with a note at the top: `> Merged into [[target-page]].`

## Constraints
- Never modify files in `raw/`
- Never delete wiki pages — mark as merged or deprecated instead
- Do not create pages for concepts mentioned only once