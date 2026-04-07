# Notes from Claude

Observations and suggestions for the wiki owner.

---

## 2026-04-07

### Pages created from raw/ sources

All 3 PDFs and the iso-merging codebase have been processed into `wiki/ai/`:

| Page | Source(s) |
|------|-----------|
| `wiki/ai/model-merging.md` | all 3 PDFs, iso-merging/ |
| `wiki/ai/task-vectors.md` | 2412.00081, 2502.04959, 2503.18063 |
| `wiki/ai/task-interference.md` | 2412.00081, 2502.04959 |
| `wiki/ai/subspace-alignment.md` | 2502.04959 |
| `wiki/ai/task-singular-vectors.md` | 2412.00081, iso-merging/ |
| `wiki/ai/isotropic-model-merging.md` | 2502.04959, iso-merging/ |
| `wiki/ai/dynamic-task-vector-grouping.md` | 2503.18063 |

PDF identities confirmed:
- `raw/2412.00081v3.pdf` — "Task Singular Vectors" (Gargiulo et al., Sapienza, arXiv 2412.00081)
- `raw/2502.04959v3.pdf` — "No Task Left Behind: Isotropic Model Merging" (Marczak et al., ICML 2025, arXiv 2502.04959)
- `raw/2503.18063v1.pdf` — "Dynamic Task Vector Grouping" (Zhang et al., Beihang, arXiv 2503.18063)

### Gaps and suggested pages

The following methods are mentioned across multiple pages but have no dedicated page:

- **TIES Merging** (Yadav et al., 2023) — resolves sign conflicts in task vectors before merging; cited in all three papers. Worth a page when you encounter the original paper.
- **Consensus Merging** (Wang et al., 2024) — removes parameters important to fewer than two tasks; frequently used as a strong baseline. Same suggestion.

### Potential connections to explore

- The DTVG paper (2503.18063) cites "Belanec et al., 2024" as concurrent work using task prompt vectors for generalization to new target tasks without training. This may be your own prior work — worth checking and linking if so.
- `raw/iso-merging/` implements several baseline methods beyond Iso-C/CTS (TIES, Consensus, TALL-Mask, TSVM). The code could be referenced on those pages once they exist.

---

## 2026-04-07 — Wiki audit

### Fix: stale index header

`wiki/model-merging/index.md` still has the old title and heading from when the topic was `ai/`. Change:
- frontmatter `title:` → `Model Merging — Topic Index`
- `# AI` → `# Model Merging`
- summary blockquote → something specific to model merging

### Pages to expand

**`task-vectors.md`** (thinnest content page) is missing the full algebra of task vectors from the original Task Arithmetic paper:
- Negation: `-τ` forgets a task (the merged model degrades on it)
- Composition: task vectors from unrelated tasks can be added; from conflicting tasks they cancel
- These operations motivate why interference matters and make the page self-contained

**`model-merging.md`** describes TIES Merging and Consensus Merging in one line each with no mechanism. Until they get their own pages, expand those entries here with at least a sentence on how each works.

### No merge candidates

All seven pages cover distinct enough concepts. `subspace-alignment` and `task-interference` are complementary perspectives on the same problem but are already cross-linked and clearly delineated — keep separate.

### No missing backlinks

Automated check confirmed all declared backlinks match actual outgoing links across all pages.

### Topical misfit to watch

`dynamic-task-vector-grouping.md` is about multi-task **prompt tuning**, not weight-space model merging. It sits in `model-merging/` because the 3-page rule prevents creating `prompt-tuning/` yet. If you add more prompt-tuning or PEFT papers, move it then.

### Approximate results in isotropic-model-merging.md

The results table uses `~89%` and `~87%` for Iso-CTS at 8 and 14 tasks. Verify exact figures from Table 1 of `raw/2502.04959v3.pdf` and update.
