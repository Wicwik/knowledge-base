---
title: Dynamic Task Vector Grouping (DTVG)
topic: model-merging
created: 2026-04-07
updated: 2026-04-07
---

# Dynamic Task Vector Grouping (DTVG)

> A multi-task prompt tuning method that dynamically selects and merges a subset of source tasks to improve low-resource target task performance. Unlike methods that transfer from one or all source tasks, DTVG groups a subset using target similarity and knowledge consistency metrics, updating the group at each training iteration.

## Content

### Problem Setting

In multi-task prompt tuning, soft prompts learned on high-resource source tasks are transferred to improve low-resource target tasks. Two standard approaches exist:
- **One-for-one**: Transfer the prompt from the single most similar source task
- **All-for-one**: Learn a shared prompt from all source tasks

DTVG shows that neither extreme is optimal — the best transfer comes from a **subset** of source tasks, and the optimal subset **changes during training** as the target prompt converges.

### Method

#### Stage 1: Task Prompt Vector Learning
Each source and target task is individually prompt-tuned from a shared initialization P_init. The task prompt vector (TPV) is defined as T = P_learned − P_init, analogous to [[model-merging/task-vectors]] in weight space.

#### Stage 2: Multi-task Prompt Transfer (Iterative)

At each training step:
1. **Task similarity**: Compute dot-product similarity between averaged token-wise TPVs (more reliable than cosine similarity between prompts used by SPoT)
2. **Source task grouping**: Select source tasks maximizing a combined objective: Target Similarity (TS) + λ·Knowledge Consistency (KC), where KC penalizes conflicts between source tasks. A greedy heuristic approximates this NP-hard selection.
3. **Multi-task merging**: Merge selected task prompt vectors with learned per-token scaling α:
   P_mix = P_init + α_t·T_t + Σ α_s·T_s

The group is dynamically updated each iteration. In practice, grouping stabilizes after initial fluctuations as the target prompt converges.

### Key Results

- Achieves SOTA on GLUE (87.4 avg) and SuperGLUE (78.9 avg) with only 0.035% parameter tuning
- Dramatic improvements on low-resource tasks: CoLA jumps from 10.6% (vanilla PT) to 69.1%
- Generalizes to decoder-based LLMs (Llama-3.2-1B/3B) and NLG tasks
- Avoids negative transfer that SPoT suffers on some datasets (e.g., WSC, CB)

### Relationship to Model Merging

DTVG applies [[model-merging/task-vectors]] arithmetic in prompt space rather than full weight space. While [[model-merging/model-merging]] methods like [[model-merging/task-singular-vectors]] and [[model-merging/isotropic-model-merging]] merge full model weights post-hoc, DTVG merges during training and operates only on the small prompt parameter space. The [[model-merging/task-interference]] problem appears in both settings — DTVG addresses it through selective grouping rather than SVD-based decorrelation.

## See Also

- [[model-merging/task-vectors]]
- [[model-merging/model-merging]]
- [[model-merging/task-interference]]

## Backlinks

- [[model-merging/model-merging]]
- [[model-merging/task-vectors]]

## Sources

- raw/2503.18063v1.pdf
