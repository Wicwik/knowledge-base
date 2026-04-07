---
title: Model Merging
topic: ai
created: 2026-04-07
updated: 2026-04-07
---

# Model Merging

> Combining the weights of multiple task-specific neural networks into a single multi-task model without additional training. The merged model ideally performs well on all constituent tasks while requiring only one set of weights at inference time.

## Content

Model merging addresses a practical problem: given several models fine-tuned from the same pre-trained checkpoint on different tasks, how to produce one model that handles all tasks. This avoids the cost of ensembling (running multiple models) or multi-task training (retraining from scratch).

### Core Setup

Starting from a pre-trained model with weights θ₀, each task t yields fine-tuned weights θₜ. The goal is to find a merging function f such that:

θ_M = f(θ₀, {θₜ})

performs well on all T tasks.

### Key Methods

- **Weight Averaging**: Simply average all fine-tuned weights. Surprisingly effective as a baseline.
- **[[ai/task-vectors]]** (Task Arithmetic): Compute task vectors τᵢ = θₜ − θ₀, then combine as θ_M = θ₀ + α·mean(τᵢ). Introduced by Ilharco et al. (2023).
- **TIES Merging**: Resolves sign conflicts in task vectors before averaging, trimming low-magnitude parameters.
- **Consensus Merging**: Retains only parameter updates where tasks agree on the direction of change.
- **[[ai/task-singular-vectors]]** (TSV-M): Decomposes per-layer task matrices via SVD, compresses, and decorrelates singular vectors to reduce [[ai/task-interference]].
- **[[ai/isotropic-model-merging]]** (Iso-C / Iso-CTS): Flattens the singular value spectrum of merged task matrices to improve [[ai/subspace-alignment]] across tasks.

### Why It Works

Fine-tuned models from a shared pre-trained checkpoint tend to lie in a connected low-loss basin. Task-specific weight changes are often approximately orthogonal, meaning they occupy different subspaces and can be superimposed without catastrophic interference — though reducing the remaining [[ai/task-interference]] is the focus of recent methods.

### Evaluation

Standard benchmarks merge CLIP ViT models fine-tuned on 8, 14, or 20 vision classification tasks (Cars, DTD, EuroSAT, GTSRB, MNIST, RESISC45, SVHN, SUN397, etc.). Performance is measured by average absolute accuracy and average normalized accuracy (relative to individually fine-tuned models).

## See Also

- [[ai/task-vectors]]
- [[ai/task-interference]]
- [[ai/task-singular-vectors]]
- [[ai/isotropic-model-merging]]
- [[ai/subspace-alignment]]
- [[ai/dynamic-task-vector-grouping]]

## Backlinks

- [[ai/dynamic-task-vector-grouping]]
- [[ai/isotropic-model-merging]]
- [[ai/subspace-alignment]]
- [[ai/task-interference]]
- [[ai/task-singular-vectors]]
- [[ai/task-vectors]]

## Sources

- raw/2412.00081v3.pdf
- raw/2502.04959v3.pdf
- raw/2503.18063v1.pdf
- raw/iso-merging/
