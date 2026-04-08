---
title: Model Merging
topic: model-merging
created: 2026-04-07
updated: 2026-04-08
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
- **[[model-merging/task-vectors]]** (Task Arithmetic): Compute task vectors τᵢ = θₜ − θ₀, then combine as θ_M = θ₀ + α·mean(τᵢ). Introduced by Ilharco et al. (2023).
- **TIES Merging** (Yadav et al., 2023): Three-step process — (1) trim parameters with low-magnitude updates (below a threshold), (2) resolve sign conflicts by majority vote across tasks, (3) average only the parameters that agree on sign. Substantially reduces interference compared to naive averaging.
- **Consensus Merging** (Wang et al., 2024): Removes a parameter's update entirely if fewer than k tasks assign it non-negligible magnitude, keeping only parameters with broad task consensus. Treats parameters where tasks disagree as noise rather than signal.
- **[[model-merging/task-singular-vectors]]** (TSV-M): Decomposes per-layer task matrices via SVD, compresses, and decorrelates singular vectors to reduce [[model-merging/task-interference]].
- **[[model-merging/isotropic-model-merging]]** (Iso-C / Iso-CTS): Flattens the singular value spectrum of merged task matrices to improve [[model-merging/subspace-alignment]] across tasks.

### Why It Works

Fine-tuned models from a shared pre-trained checkpoint tend to lie in a connected low-loss basin. Task-specific weight changes are often approximately orthogonal, meaning they occupy different subspaces and can be superimposed without catastrophic interference — though reducing the remaining [[model-merging/task-interference]] is the focus of recent methods.

### Evaluation

Standard benchmarks merge CLIP ViT models fine-tuned on 8, 14, or 20 vision classification tasks (Cars, DTD, EuroSAT, GTSRB, MNIST, RESISC45, SVHN, SUN397, etc.). Performance is measured by average absolute accuracy and average normalized accuracy (relative to individually fine-tuned models).

## See Also

- [[model-merging/task-vectors]]
- [[model-merging/task-interference]]
- [[model-merging/task-singular-vectors]]
- [[model-merging/isotropic-model-merging]]
- [[model-merging/subspace-alignment]]
- [[model-merging/dynamic-task-vector-grouping]]

## Backlinks

- [[model-merging/dynamic-task-vector-grouping]]
- [[model-merging/isotropic-model-merging]]
- [[model-merging/subspace-alignment]]
- [[model-merging/task-interference]]
- [[model-merging/task-singular-vectors]]
- [[model-merging/task-vectors]]

## Sources

- raw/2412.00081v3.pdf
- raw/2502.04959v3.pdf
- raw/2503.18063v1.pdf
- raw/iso-merging/
