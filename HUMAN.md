# Notes from Claude

Observations and suggestions for the wiki owner.

---

## 2026-04-07

### Pages created from raw/ sources

All 3 PDFs and the iso-merging codebase have been processed into `wiki/model-merging/`:

| Page | Source(s) |
|------|-----------|
| `wiki/model-merging/model-merging.md` | all 3 PDFs, iso-merging/ |
| `wiki/model-merging/task-vectors.md` | 2412.00081, 2502.04959, 2503.18063 |
| `wiki/model-merging/task-interference.md` | 2412.00081, 2502.04959 |
| `wiki/model-merging/subspace-alignment.md` | 2502.04959 |
| `wiki/model-merging/task-singular-vectors.md` | 2412.00081, iso-merging/ |
| `wiki/model-merging/isotropic-model-merging.md` | 2502.04959, iso-merging/ |
| `wiki/model-merging/dynamic-task-vector-grouping.md` | 2503.18063 |

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

## 2026-04-08 — Processing run

### New raw files

`raw/clippings/The Synthetic Data Playbook...md` — page failed to load when scraped (body is just "Refreshing"). No usable content. Re-scrape or fetch the URL manually when you want to process this.

### Completed from prior audit

- Fixed stale `wiki/ai/` paths in HUMAN.md (now `wiki/model-merging/`)
- Fixed stale index header in `wiki/model-merging/_index.md`
- Added Task Arithmetic algebra (negation, composition, cancellation) to `task-vectors.md`
- Expanded TIES and Consensus Merging descriptions in `model-merging.md`

### Still open

**Verify isotropic results** — `isotropic-model-merging.md` uses `~89%` and `~87%` for Iso-CTS at 8 and 14 tasks (ViT-L/14). Confirm exact figures from Table 1 of `raw/2502.04959v3.pdf`. PDFs are not readable in the current environment; do this manually.

**TIES and Consensus pages** — Both methods appear in multiple pages but have no dedicated page. Worth adding when you encounter the original papers:
- TIES: Yadav et al., 2023 (arXiv 2306.01708)
- Consensus: Wang et al., 2024

**Topical misfit** — `dynamic-task-vector-grouping.md` is about multi-task prompt tuning, not weight-space merging. Move to a `prompt-tuning/` topic if you add more PEFT/prompt-tuning papers.

**Possible self-citation** — DTVG paper (2503.18063) cites "Belanec et al., 2024" as concurrent work on task prompt vectors. Check if this is your own prior work and link if so.

---

## 2026-04-08 — Wiki audit

### Backlinks

All backlinks are correct and complete. Automated check confirmed no missing or stale entries across all seven pages.

Fixed one structural gap: `dynamic-task-vector-grouping.md` linked to `task-singular-vectors` and `isotropic-model-merging` in its body but omitted them from See Also. Added.

### Merge candidates

None. All seven pages cover distinct enough concepts. `subspace-alignment` and `task-interference` are the closest pair but are clearly delineated (SAR measures representation coverage; STI measures direct singular vector overlap) and already cross-linked.

### Stubs to expand

**`dynamic-task-vector-grouping.md`** — the Knowledge Consistency (KC) metric is named and described in prose ("penalizes conflicts between source tasks") but never defined. The paper must give a formula. Worth adding when you re-read 2503.18063.

**`subspace-alignment.md`** — shortest page (57 lines). Correct but thin. Could add:
- Practical interpretation: what does a SAR of 0.3 vs 0.8 imply for merged accuracy?
- Per-task SAR breakdown for the 8-task CLIP benchmark (which tasks have low vs high SAR under Task Arithmetic?) — figures in 2502.04959

### Approximate results (still open)

`isotropic-model-merging.md` results table uses `~89%` and `~87%` for Iso-CTS at 8 and 14 tasks on ViT-L/14. The 20-task figure (90.1%) appears exact. Verify all three from Table 1 of `raw/2502.04959v3.pdf` manually.
