---
title: Isotropic Model Merging
topic: ai
created: 2026-04-07
updated: 2026-04-07
---

# Isotropic Model Merging

> A [[ai/model-merging]] framework that flattens (makes isotropic) the singular value spectrum of merged task matrices. Iso-C equalizes the common subspace; Iso-CTS additionally incorporates task-specific subspaces for directions underrepresented in the common space. Achieves state-of-the-art on vision and language merging benchmarks (ICML 2025).

## Content

### Motivation

When task matrices are summed (Task Arithmetic), the resulting merged matrix has a skewed singular value spectrum — a few dominant directions dominate while others are suppressed. This means tasks whose important directions align with the dominant ones perform well, while tasks with orthogonal or weaker directions are left behind. The key finding: **[[ai/subspace-alignment]] between task-specific and merged matrices strongly correlates with merged model performance** (Pearson ρ = 0.94).

### Iso-C (Common Subspace)

1. Sum task matrices: Δ_TA = Σ Δₜ
2. Compute SVD: Δ_TA = UΣVᵀ
3. Replace all singular values with their mean: σ̄ = (1/r)·Σσᵢ
4. Reconstruct: Δ_Iso-C = σ̄ · U · Vᵀ

This simple operation — replacing the spectrum with a flat one while keeping the singular vectors — substantially improves [[ai/subspace-alignment]] and accuracy. It equalizes representation across all tasks in the shared subspace.

Implementation in `raw/iso-merging/src/utils/iso.py`: averages the task matrices per layer, computes SVD, then reconstructs with `S_mean = ones_like(S) * S.mean()`.

### Iso-CTS (Common + Task-Specific Subspaces)

As the number of tasks grows, some task-specific directions are orthogonal to the common subspace and remain underrepresented even after isotropic scaling. Iso-CTS addresses this:

1. Retain top-k singular vectors from the common subspace (from Δ_TA sum)
2. For each task, project out the common subspace: w_ts = w − U_common U_commonᵀ w
3. Compute SVD of each task's residual; retain s = (r−k)/T per-task components
4. Concatenate common + task-specific vectors into combined U, S, V
5. Orthogonalize via whitening: U_orth = P_U Q_Uᵀ, V_orth = P_V Q_Vᵀ
6. Apply isotropic scaling: replace S with uniform mean
7. Reconstruct the merged matrix

The `common_space_fraction` hyperparameter (default 0.8) controls how much of the subspace is shared vs. task-specific.

### Results

| Method | 8 tasks | 14 tasks | 20 tasks |
|--------|---------|----------|----------|
| Task Arithmetic | 65.2% | 71.6% | 74.0% |
| TSV-M | 86.3% | 82.2% | 87.7% |
| Iso-CTS | **~89%** | **~87%** | **90.1%** |

(CLIP ViT-L/14, absolute accuracy; exact figures vary by paper version)

Gains are largest when the number of tasks is high and tasks are diverse, because more tasks means more underrepresented directions that benefit from task-specific subspaces.

### Code

The reference implementation is in `raw/iso-merging/`. Key files:
- `main.py`: Entry point (`python main.py method="iso_cts" model=ViT-B-16 num_tasks=8`)
- `src/utils/iso.py`: Core `iso_c` and `iso_cts` functions
- `config/method/iso_c.yaml`, `config/method/iso_cts.yaml`: Hyperparameter configs
- Uses Hydra for config management, WandB for logging

## See Also

- [[ai/model-merging]]
- [[ai/task-vectors]]
- [[ai/task-singular-vectors]]
- [[ai/task-interference]]
- [[ai/subspace-alignment]]

## Backlinks

- [[ai/dynamic-task-vector-grouping]]
- [[ai/model-merging]]
- [[ai/subspace-alignment]]
- [[ai/task-interference]]
- [[ai/task-singular-vectors]]
- [[ai/task-vectors]]

## Sources

- raw/2502.04959v3.pdf
- raw/iso-merging/
