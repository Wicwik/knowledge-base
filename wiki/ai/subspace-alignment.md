---
title: Subspace Alignment
topic: ai
created: 2026-04-07
updated: 2026-04-07
---

# Subspace Alignment

> The degree of overlap between the principal singular vector subspaces of individual task matrices and the merged matrix. High alignment correlates strongly with good merged-model performance; low alignment indicates a task is underrepresented in the merge.

## Content

### Subspace Alignment Ratio (SAR)

Introduced in the [[ai/isotropic-model-merging]] paper, SAR measures how much of a task matrix's energy is captured by the top singular vectors of the merged matrix:

SAR(Δₜ, Δ_M; k_M) = ‖Π_{k_M} Δₜ‖_F / ‖Δₜ‖_F

where Π_{k_M} is the projection onto the top k_M left-singular vectors of the merged matrix Δ_M. The number k_M is chosen to capture 95% of the merged matrix's energy.

SARavg averages this ratio across all layers to give a single score per task.

### Correlation with Performance

For CLIP ViT-B/32 with 8 tasks, the Pearson correlation between SARavg and Normalized Accuracy Improvement is ρ = 0.94 under Task Arithmetic. This means SAR nearly perfectly predicts which tasks will benefit most from merging.

### Why Some Tasks Get Left Behind

Tasks like DTD, Cars, and SUN397 have low mutual alignment with other tasks. Their important directions are underrepresented in the summed matrix because they are drowned out by the dominant directions from groups of mutually aligned tasks (e.g., {MNIST, SVHN, GTSRB}). The skewed singular value spectrum of Task Arithmetic exacerbates this.

### How Isotropic Merging Helps

Flattening the singular value spectrum (Iso-C) increases the effective number of dominant directions k, thereby raising SAR for underrepresented tasks. Adding task-specific subspaces (Iso-CTS) further boosts SAR by explicitly including directions orthogonal to the common subspace.

### Relationship to Task Interference

SAR and [[ai/task-interference]] are complementary perspectives. STI from [[ai/task-singular-vectors]] measures how much singular vectors from different tasks overlap (causing interference), while SAR measures how well each task's subspace is represented in the merged result. Reducing interference and improving alignment are two sides of the same coin.

## See Also

- [[ai/isotropic-model-merging]]
- [[ai/task-interference]]
- [[ai/task-singular-vectors]]
- [[ai/model-merging]]

## Backlinks

- [[ai/model-merging]]
- [[ai/task-interference]]
- [[ai/task-singular-vectors]]
- [[ai/isotropic-model-merging]]

## Sources

- raw/2502.04959v3.pdf
