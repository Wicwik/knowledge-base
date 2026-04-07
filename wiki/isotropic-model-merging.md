# Isotropic Model Merging

> A [[model-merging]] framework that flattens (makes isotropic) the singular value spectrum of merged task matrices. Iso-C equalizes the common subspace; Iso-CTS additionally incorporates task-specific subspaces for directions underrepresented in the common space. Achieves state-of-the-art on vision and language merging benchmarks (ICML 2025).

## Content

### Motivation

When task matrices are summed (Task Arithmetic), the resulting merged matrix has a skewed singular value spectrum — a few dominant directions dominate while others are suppressed. This means tasks whose important directions align with the dominant ones perform well, while others are underrepresented. The key finding is that **[[subspace-alignment]] between task-specific and merged matrices strongly correlates with merged model performance** (Pearson ρ = 0.94).

### Iso-C (Common Subspace)

1. Sum task matrices: Δ_TA = Σ Δₜ
2. Compute SVD: Δ_TA = UΣV^T
3. Replace all singular values with their mean: σ̄ = (1/r)·Σσᵢ
4. Reconstruct: Δ_Iso-C = σ̄ · U · V^T

This simple operation — replacing the spectrum with a flat one while keeping the singular vectors — substantially improves alignment and accuracy. It equalizes representation across all tasks in the shared subspace.

### Iso-CTS (Common + Task-Specific Subspaces)

As the number of tasks grows, some task-specific directions are orthogonal to the common subspace and remain underrepresented even after isotropic scaling. Iso-CTS addresses this:

1. Retain top-k singular vectors from the common subspace (from Δ_TA)
2. For each task, project out the common subspace and compute SVD of the residual
3. Retain s = (r−k)/T task-specific singular components per task
4. Concatenate common + task-specific vectors, orthogonalize via whitening
5. Apply isotropic scaling using the mean of all retained singular values
6. Reconstruct the merged matrix

The fraction k/r allocated to the common subspace (default 0.8) controls the balance between shared and task-specific directions.

### Results

On CLIP ViT-L/14, 20 tasks: Iso-CTS achieves 90.1% absolute accuracy (94.9% normalized) — substantially improving over TSV-M's 87.7% and Task Arithmetic's 74.0%. Gains are largest when the number of tasks is high and tasks are diverse.

### Code

The reference implementation is in `raw/iso-merging/`. Key files:
- `main.py`: Entry point for merge and evaluation
- `src/utils/iso.py`: Core isotropic merging logic
- Config files in `config/method/iso_c.yaml` and `config/method/iso_cts.yaml`

## See Also

- [[model-merging]]
- [[task-vectors]]
- [[task-singular-vectors]]
- [[task-interference]]
- [[subspace-alignment]]

## Backlinks

<!-- AUTO: pages that link to this one -->
- [[dynamic-task-vector-grouping]]
- [[model-merging]]
- [[subspace-alignment]]
- [[task-interference]]
- [[task-singular-vectors]]
- [[task-vectors]]

## Sources

- raw/2502.04959v3.pdf
- raw/iso-merging/
