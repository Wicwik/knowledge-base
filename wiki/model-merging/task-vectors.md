---
title: Task Vectors
topic: model-merging
created: 2026-04-07
updated: 2026-04-07
---

# Task Vectors

> A task vector is the element-wise difference between fine-tuned and pre-trained model weights (τ = θ_ft − θ_pre). Task Arithmetic combines these vectors to build multi-task models: θ_MT = θ_pre + α·mean(τᵢ). This simple idea is the foundation for most modern [[model-merging/model-merging]] methods.

## Content

### Definition

Given a pre-trained model θ_pre and a model θ_ft fine-tuned on task i:

τᵢ = θ_ft,i − θ_pre

This vector encodes what the model "learned" for task i in weight space.

### Task Arithmetic

The multi-task model is constructed by adding a scaled average of task vectors back to the pre-trained weights:

θ_MT = θ_pre + α · (1/T) · Σ τᵢ

where α is a scaling coefficient typically tuned on a validation set. Ilharco et al. (2023) showed this surprisingly effective despite its simplicity.

### Layer-Level View

Rather than treating the entire network as a flat vector, methods like [[model-merging/task-singular-vectors]] and [[model-merging/isotropic-model-merging]] operate on per-layer task matrices Δᵢ^(l) = θ_ft,i^(l) − θ_pre^(l), preserving the matrix structure of each layer. This enables SVD-based analysis and more targeted interference reduction.

### Limitations

- Treats all parameters equally, ignoring layer structure and importance.
- Susceptible to [[model-merging/task-interference]] when task vectors are not orthogonal.
- The scaling coefficient α requires tuning (though [[model-merging/task-singular-vectors]] finds α=1.0 works consistently with their method).

### In Prompt Tuning

[[model-merging/dynamic-task-vector-grouping]] extends the task vector concept to soft prompts, defining task prompt vectors (TPV) as the difference between learned and initial prompts. These are used to measure inter-task similarity and perform model merging in prompt space rather than full weight space.

## See Also

- [[model-merging/model-merging]]
- [[model-merging/task-interference]]
- [[model-merging/task-singular-vectors]]
- [[model-merging/isotropic-model-merging]]
- [[model-merging/dynamic-task-vector-grouping]]

## Backlinks

- [[model-merging/dynamic-task-vector-grouping]]
- [[model-merging/isotropic-model-merging]]
- [[model-merging/model-merging]]
- [[model-merging/task-interference]]
- [[model-merging/task-singular-vectors]]

## Sources

- raw/2412.00081v3.pdf
- raw/2502.04959v3.pdf
- raw/2503.18063v1.pdf
