# Task Interference

> When task vectors from different tasks share overlapping directions in weight space, merging them causes destructive interaction — degrading performance on individual tasks. Measuring and reducing this interference is the central challenge in [[model-merging]].

## Content

### The Problem

Task interference occurs because the weight updates learned for different tasks are not perfectly orthogonal. When summed during merging, overlapping components can:
- Cancel each other (destructive interference from opposing directions)
- Amplify shared directions at the expense of task-specific ones
- Corrupt the merged model's representations for individual tasks

### Measuring Interference

**Cosine similarity** between flattened task vectors was the original metric (Ilharco et al., 2023), but it provides only a coarse, global view.

**Singular Task Interference (STI)** from [[task-singular-vectors]] provides a layer-level measure based on the alignment of left and right singular vectors across tasks:

STI({Δᵢ}) = ‖(U^T U − I) Σ (V^T V − I)‖₁

High off-diagonal values in U^T U or V^T V indicate overlapping singular subspaces and thus higher interference. STI reveals that interference is highest in early transformer layers (which capture shared features) and lowest in deeper, more specialized layers.

**[[subspace-alignment]] Ratio (SAR)** from [[isotropic-model-merging]] measures how well the merged matrix's top singular vectors cover each individual task's subspace. Low SAR for a task means the merged model underrepresents that task.

### Reducing Interference

- **TIES Merging**: Trims low-magnitude values, resolves sign conflicts.
- **Consensus Merging**: Keeps only parameter directions where all tasks agree.
- **TSV-M** ([[task-singular-vectors]]): Low-rank compression followed by whitening transformation to decorrelate singular vectors across tasks.
- **Iso-C / Iso-CTS** ([[isotropic-model-merging]]): Flattens the singular value spectrum to equalize representation of all task directions.

### Key Insight

Combining low-rank compression with interference reduction is synergistic: low-rank matrices can be orthogonalized with much less approximation error than full-rank ones, leading to substantially better results when both steps are applied together (TSV-M ablation: +15.8 to +17.5 points over baseline).

## See Also

- [[model-merging]]
- [[task-vectors]]
- [[task-singular-vectors]]
- [[isotropic-model-merging]]
- [[subspace-alignment]]

## Backlinks

<!-- AUTO: pages that link to this one -->
- [[dynamic-task-vector-grouping]]
- [[isotropic-model-merging]]
- [[model-merging]]
- [[subspace-alignment]]
- [[task-singular-vectors]]
- [[task-vectors]]

## Sources

- raw/2412.00081v3.pdf
- raw/2502.04959v3.pdf
