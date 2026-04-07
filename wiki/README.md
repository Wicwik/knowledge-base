# wiki/

The compiled knowledge base. Permanent, linked, rewritten from sources in your own words.

**What belongs here:** One Markdown page per concept, organized into narrow project-oriented topic subdirectories (e.g. `wiki/model-merging/`). Each page has frontmatter, a blockquote summary, and cross-links to related pages. Each topic directory has its own `index.md`; the root `wiki/index.md` lists all topics.

**Who fills it:** Claude, based on sources you put in `raw/`.

**Example:**

```
wiki/model-merging/task-vectors.md
---
title: Task Vectors
topic: model-merging
created: 2026-04-07
updated: 2026-04-07
---

# Task Vectors

> The weight-space difference between a fine-tuned and pre-trained model...
```
