---
title: Model Merging — Topic Index
topic: model-merging
created: 2026-04-07
updated: 2026-04-07
---

# Model Merging

> Techniques for combining multiple fine-tuned models into a single multi-task model without retraining, and related concepts around task vectors, interference, and subspace analysis.

## Pages

| Page | Summary |
|------|---------|
| [[model-merging/index]] | *(this page)* Topic index |
| [[model-merging/model-merging]] | Combining fine-tuned model weights into a single multi-task model without retraining |
| [[model-merging/task-vectors]] | Weight-space differences between fine-tuned and pre-trained models; foundation of Task Arithmetic |
| [[model-merging/task-interference]] | Destructive interaction when overlapping task directions are merged; metrics and mitigations |
| [[model-merging/subspace-alignment]] | Overlap between task and merged matrix subspaces; predicts per-task merging quality (SAR, ρ=0.94) |
| [[model-merging/task-singular-vectors]] | Per-layer SVD of task matrices enabling 10× compression and whitening-based interference reduction |
| [[model-merging/isotropic-model-merging]] | Flattening singular value spectrum (Iso-C) and adding task-specific subspaces (Iso-CTS) for SOTA merging |
| [[model-merging/dynamic-task-vector-grouping]] | Dynamic source-task selection for multi-task prompt tuning using task prompt vectors |
