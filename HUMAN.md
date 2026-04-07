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
