# Task Singular Vectors (TSV)

> A model merging framework that decomposes per-layer task matrices via SVD, revealing that they are low-rank. TSV-Compress (TSV-C) shrinks task vectors to 10% of their size while retaining 99% accuracy. TSV-Merge (TSV-M) combines compression with a whitening-based decorrelation step that reduces [[task-interference]], achieving ~15% accuracy gains over standard [[task-vectors]].

## Content

### Core Idea

Instead of treating task vectors as flat vectors, TSV preserves the matrix structure of each layer and applies Singular Value Decomposition. The resulting singular vectors — termed Task Singular Vectors — reveal:
1. Per-layer task matrices are **low-rank**: ~3% of singular components capture nearly all task-specific information.
2. The **alignment of singular vectors** across tasks determines the degree of [[task-interference]].

### TSV-Compress (TSV-C)

Retains only the top 1/T fraction of singular components per task (where T = number of tasks):
- Compresses task vectors to ~10% of original size
- Preserves >99% of individually fine-tuned accuracy
- Discards directions that overlap with other tasks' subspaces

### TSV-Merge (TSV-M)

Builds on TSV-C with an additional interference reduction step:

1. Compute SVD of each per-layer task matrix
2. Retain top 1/T singular components per task
3. Concatenate the reduced U, Σ, V matrices across tasks
4. Apply whitening (Procrustes orthogonalization) to decorrelate the concatenated singular vectors
5. Reconstruct the merged layer matrix

The whitening step via SVD of the concatenated matrices (U = P_U D_U Q_U^T) yields orthogonal matrices U_⊥ = P_U Q_U^T that minimize interference.

### Key Results

On CLIP ViT-L/14 with 8 tasks: 92.98% absolute accuracy (96.98% normalized), meaning the single merged model retains ~97% of the performance of 8 individual models.

The method consistently achieves best results with α=1.0 (no scaling needed), eliminating the hyperparameter tuning required by other methods.

### Relationship to Iso-CTS

[[isotropic-model-merging]] builds on TSV's insights, sharing the SVD decomposition and whitening steps but adding isotropic scaling (flattening singular values) and an explicit common+task-specific subspace decomposition for further gains.

## See Also

- [[model-merging]]
- [[task-vectors]]
- [[task-interference]]
- [[isotropic-model-merging]]
- [[subspace-alignment]]

## Backlinks

<!-- AUTO: pages that link to this one -->
- [[dynamic-task-vector-grouping]]
- [[isotropic-model-merging]]
- [[model-merging]]
- [[subspace-alignment]]
- [[task-interference]]
- [[task-vectors]]

## Sources

- raw/2412.00081v3.pdf
- raw/iso-merging/
