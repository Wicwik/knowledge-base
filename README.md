# Personal Knowledge Base

A personal wiki maintained collaboratively with Claude Code. You drop source material into `raw/`; Claude reads it and compiles permanent, cross-linked notes into `wiki/`.

## Structure

```
raw/                  # source documents (gitignored — PDFs, repos, clippings)
daily/                # fleeting daily notes
wiki/                 # compiled knowledge base, organized by topic
  model-merging/      # example topic directory
    index.md          # topic index
    task-vectors.md   # one concept per page
resources/
  queue/              # things to read
  archive/            # things you've read
projects/             # one page per active project
archive/              # completed/dormant projects and deprecated pages
CLAUDE.md             # instructions auto-loaded by Claude Code
HUMAN.md              # Claude's notes and suggestions for you
```

Each directory contains a `README.md` with more detail.

## Usage

1. **Add a source** — put a PDF, cloned repo, or clipping into `raw/`
2. **Ask Claude to process it** — open Claude Code in this repo and say e.g. *"process all new files in raw/ into the wiki"*
3. **Browse** — start at `wiki/index.md` and follow cross-links, or open any topic directory

Claude handles extraction, page creation, cross-linking, indexing, and committing. You handle curation: deciding what's worth reading, moving resources from `queue/` to `archive/`, and maintaining `projects/`.

## Bootstrapping

To set this up in a new environment:

```bash
git clone <repo-url>
cd <repo>

# Install Claude Code if you don't have it
npm install -g @anthropic/claude-code

# Launch Claude Code in this directory
claude
```

Claude Code will auto-load `CLAUDE.md` and have full context of the wiki conventions. Drop files into `raw/` and ask it to process them.

> **Note:** `raw/` is gitignored. You are responsible for keeping source documents on your own machine or syncing them separately.
