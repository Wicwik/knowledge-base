# Spectral-Guided Bayesian Task Selection for Multi-Task Parameter-Efficient Fine-Tuning

## A Research Paper Outline & Experimental Plan

---

## 1. Proposed Title Options

1. **"Spectral-Guided Bayesian Task Selection for Multi-Task Parameter-Efficient Fine-Tuning"**
2. _"Beyond Similarity Heuristics: Geometry-Aware Source Task Selection for Multi-Task Transfer"_
3. _"From PEFT to Full Fine-Tuning: Subspace-Aware Task Selection via Bayesian Optimization"_

---

## 2. Abstract (Draft)

Source task selection is a critical yet underexplored bottleneck in multi-task transfer learning. Existing methods rely on heuristic similarity metrics — such as cosine similarity or dot-product between task representations — to select source tasks, but these metrics correlate poorly with actual transfer performance. We propose **SpectralBO**, a geometry-aware task selection framework that combines (1) Subspace Alignment Ratio (SAR) as a principled, spectrally-grounded selection criterion, (2) multi-fidelity multi-task Bayesian optimization to efficiently search the combinatorial space of source task subsets, and (3) isotropic merging to mitigate interference among selected source tasks. Our framework operates across multiple PEFT methods (LoRA, prompt tuning, adapters) and investigates whether task selection decisions made in the cheap PEFT regime transfer to full fine-tuning. Experiments on [X] NLP benchmarks with up to [Y] source tasks demonstrate that SpectralBO consistently outperforms similarity-based heuristics, and that PEFT-based task selection reliably predicts optimal full fine-tuning configurations, enabling a practical and cost-effective pipeline for large-scale multi-task transfer.

---

## 3. Paper Structure

### Section 1: Introduction

**Key points to make:**

- Multi-task transfer learning improves low-resource target tasks, but source task selection is critical — wrong selections cause negative transfer.
- Current approaches fall into three camps:
    - "One for one" (select single best source) — misses complementary combinations
    - "All for one" (use all sources) — suffers from interference
    - "Part for one" (select subset) — DTVG does this but relies on dot-product heuristics
- **Gap 1**: Similarity heuristics are poor predictors of transfer performance (Iso-CTS shows cosine similarity is uncorrelated with NAI).
- **Gap 2**: Existing methods are specific to one PEFT method (DTVG = prompt tuning only). Unknown if insights generalize across LoRA, adapters, full FT.
- **Gap 3**: No principled search over the combinatorial subset space — DTVG uses a greedy heuristic for an NP-hard problem.
- **Our contributions** (see Section 4 below).

**Motivating figure (Figure 1):** A 2×2 showing: (a) dot-product similarity vs. transfer performance (low correlation), (b) SAR vs. transfer performance (high correlation), for both prompt tuning and LoRA — demonstrating that SAR generalizes as a selection criterion.

---

### Section 2: Related Work

**2.1 Multi-Task Transfer Learning**

- Full FT multi-task: HyperFormer, HyperDecoder
- PEFT multi-task: SPoT, ATTEMPT, MPT, TPT, DTVG
- Position our work as extending beyond prompt tuning

**2.2 Task Selection and Task Similarity**

- Task2Vec (Achille et al., 2019)
- Taskonomy (Zamir et al., 2018)
- Intermediate task transfer (Pruksachatkun et al., 2020; Vu et al., 2020)
- DTVG's task prompt vector similarity
- Our critique: these are all proxy metrics; we propose geometry-aware selection

**2.3 Model Merging**

- Task Arithmetic (Ilharco et al., 2023)
- TIES-Merging (Yadav et al., 2023)
- Iso-CTS (Marczak et al., 2025) — isotropic merging, SAR metric
- TSV (Gargiulo et al., 2025)
- Position: we are the first to combine principled task _selection_ with interference-aware _merging_

**2.4 Bayesian Optimization for AutoML**

- BO for hyperparameter optimization
- Multi-task BO (Swersky et al., 2013)
- Multi-fidelity BO (FABOLAS, Freeze-Thaw)
- Position: first application of BO to source task subset selection

---

### Section 3: Background

**3.1 Problem Formulation**

- Source task set S = {s₁, ..., sₙ}, target task t
- Goal: find S' ⊆ S that maximizes performance on t after transfer
- Combinatorial search space: 2ⁿ - 1 subsets

**3.2 PEFT Methods**

- Prompt tuning: task prompt vectors T = P* - P_init
- LoRA: task matrices ΔW = BA
- Adapters: task-specific bottleneck layers
- Unified view: all produce a "task matrix" ΔW relative to the pretrained model

**3.3 Subspace Alignment Ratio (SAR)**

- Definition from Iso-CTS (Equation 5 in their paper)
- Strong correlation with transfer performance (ρ = 0.94)
- Computable without training — just SVD on task matrices

**3.4 Isotropic Merging**

- Iso-C: flatten singular value spectrum of merged matrix
- Iso-CTS: combine common subspace + task-specific directions
- Key insight: skewed spectra favor dominant tasks, harming underrepresented ones

---

### Section 4: Method — SpectralBO

**4.1 Overview**

The SpectralBO pipeline has four stages:

```
Stage 1: Independent PEFT Training
  → Train each source & target task independently with PEFT
  → Extract task matrices {Δ₁, ..., Δₙ, Δₜ}
  → One-time cost, reusable across all target tasks

Stage 2: SAR-Guided Bayesian Optimization
  → Define surrogate objective: SAR(Δₜ, Δ_merged(S'))
  → Use multi-task BO to search over subsets S'
  → Leverage cross-target correlations for efficiency

Stage 3: Isotropic Merging of Selected Subset
  → Apply Iso-C or Iso-CTS to merge selected source task matrices
  → Transfer merged representation to initialize target task

Stage 4: Multi-Fidelity Validation (PEFT → Full FT)
  → Validate top-k configurations with actual PEFT training
  → Optionally validate with full FT to assess transferability
```

**4.2 SAR as Task Selection Criterion**

For a candidate subset S', we compute:

1. Merged task matrix: Δ_merged = Σᵢ∈S' Δᵢ
2. SVD of Δ_merged → top-k singular vectors
3. SAR(Δₜ, Δ_merged; k) = ||Π_k Δₜ||_F / ||Δₜ||_F

This measures how well the merged subspace of S' covers the target task's important directions.

**Key advantages over DTVG's dot-product metric:**

- Captures structural/geometric relationships, not just scalar similarity
- Accounts for how tasks interact when merged (not just pairwise)
- Shown to correlate strongly with actual performance (ρ = 0.94 vs. low for cosine sim)

**Extension — SAR with Knowledge Consistency (SAR-KC):** Inspired by DTVG's knowledge consistency, we can also compute pairwise SAR within the selected subset to measure internal coherence:

SAR-KC(S') = (2 / |S'|(|S'|-1)) Σᵢ<ⱼ SAR(Δᵢ, Δⱼ)

Combined objective: maximize SAR(Δₜ, Δ_merged(S')) + λ · SAR-KC(S')

**4.3 Multi-Task Bayesian Optimization**

**Input space**: Binary vector b ∈ {0,1}ⁿ encoding which source tasks are included.

**Objective**: f(b, t) = SAR(Δₜ, Δ_merged(S'_b))

- Cheap to evaluate: just linear algebra (SVD), no training required
- Can optionally mix in actual PEFT validation performance for top candidates

**Multi-task GP surrogate**:

- Model f(b, t) jointly across all target tasks
- Kernel: k((b₁, t₁), (b₂, t₂)) = k_subset(b₁, b₂) · k_task(t₁, t₂)
- k_subset: Hamming kernel, weighted Jaccard, or RBF on binary vectors
- k_task: learned from task embeddings or SAR matrix between targets

**Acquisition function**: Multi-task Expected Improvement

- Select the (b, t) pair that maximizes expected improvement
- Information gained for one target transfers to others via the task kernel

**Budget allocation**:

- Phase A (SAR-only, ~80% budget): Evaluate SAR — essentially free, limited by BO overhead
- Phase B (validation, ~20% budget): Train PEFT for top-k subsets per target, get actual performance

**4.4 Isotropic Merging of Selected Subsets**

After selecting S' for target task t:

1. Compute Δ_merged = Σᵢ∈S' Δᵢ
2. SVD: Δ_merged = UΣV⊤
3. Flatten spectrum: σ̄ = (1/r) Σᵢ σᵢ
4. Reconstruct: Δ_Iso = σ̄ · U · V⊤

For diverse or large S', use Iso-CTS:

- Retain top-k common directions from Δ_merged
- Add per-task orthogonal directions for each selected source
- Orthogonalize and apply isotropic scaling

This is the first framework to combine intelligent _selection_ with interference-aware _merging_.

**4.5 Cross-PEFT and PEFT-to-Full-FT Transferability**

**Research question**: Do optimal source task subsets identified under one PEFT method also work for other PEFT methods and for full fine-tuning?

**Approach**:

- Run the full SpectralBO pipeline under LoRA, prompt tuning, and adapters independently
- Compare the selected subsets: do they agree? (Jaccard similarity, rank correlation)
- For the top subsets from each PEFT method, run full fine-tuning
- Measure SAR correlation between PEFT and full FT task matrices
- If PEFT selections transfer → cheap proxy pipeline confirmed
- If not → characterize divergence patterns

**Multi-fidelity formulation**:

- Fidelity 1 (cheapest): SAR computed on LoRA task matrices
- Fidelity 2 (cheap): Actual LoRA training + evaluation
- Fidelity 3 (expensive): Full fine-tuning + evaluation
- Use multi-fidelity BO to model correlations between fidelities

---

### Section 5: Experimental Setup

**5.1 Datasets and Tasks**

Following DTVG for comparability:

**Source tasks (6 baseline, expandable to 15+)**:

- Baseline 6: MNLI, QNLI, QQP, SST2, ReCoRD, SQuAD
- Extended 15: Add SNLI, ANLI, PAWS, WinoGrande, HellaSwag, ARC, PIQA, BoolQ, MultiRC
- Extended 20+: Add NQ, HotpotQA, SearchQA, NewsQA, CosmosQA, SocialIQA

**Target tasks**:

- GLUE: RTE, CoLA, STS-B, MRPC (low-resource)
- SuperGLUE: MultiRC, BoolQ, WiC, WSC, CB
- MRQA: NQ, HotpotQA, SearchQA, NewsQA
- Other: WinoGrande, YelpPolarity, SciTail, PAWS

**5.2 Models**

- T5-base (220M) — primary, matches DTVG
- T5-large (770M) — scaling analysis
- LLaMA-3.2-1B / 3B — generalization to decoder models
- (Optional) LLaMA-3.1-8B — larger scale

**5.3 PEFT Methods**

|Method|Config|Params per task|
|---|---|---|
|Prompt Tuning|length=100|~77K|
|LoRA|rank=16, α=32|~3.8M|
|Adapters|bottleneck=64|~1.9M|
|Full FT|all params|220M+|

**5.4 Baselines**

|Category|Method|Description|
|---|---|---|
|No transfer|PT, LoRA, Adapter, FT|Single-task baselines|
|One-for-one|SPoT|Cosine similarity selection|
|All-for-one|ATTEMPT, MPT, TPT|Use all source tasks|
|Part-for-one|DTVG|Dot-product + greedy grouping|
|Merging|Task Arithmetic|Sum all task vectors|
|Merging|Iso-C / Iso-CTS|Isotropic merging (all tasks)|
|Ours|SpectralBO|SAR + BO selection + Iso merging|

**5.5 Evaluation Metrics**

- **Primary**: Average accuracy/F1 across target tasks
- **Per-task**: Individual task performance to detect "no task left behind"
- **Selection quality**: Jaccard similarity between selected subsets across methods
- **Efficiency**: Number of evaluations needed to find top subset
- **Transferability**: Rank correlation of subset rankings between PEFT and full FT

**5.6 Implementation Details**

- BO: BoTorch with multi-output GP, Hamming kernel for subsets
- Training: HuggingFace PEFT library
- SAR computation: Custom SVD pipeline (NumPy/PyTorch)
- Seeds: 4 random seeds per experiment, report mean ± std
- Compute: Single A100 GPU for most experiments

---

### Section 6: Experiments and Results

**6.1 Main Results: SpectralBO vs. Baselines**

**Table 1**: Full-dataset transfer on GLUE + SuperGLUE (T5-base)

- Rows: all baselines + SpectralBO variants
- Columns: each target task + average
- Key claim: SpectralBO outperforms DTVG and all-for-one baselines

**Table 2**: Extended source pool (15 sources)

- Key claim: SpectralBO's advantage grows with more source tasks (where greedy heuristics fail)

**Table 3**: Few-shot adaptation (k = 4, 16, 32)

- Key claim: SpectralBO is especially beneficial in low-resource settings

**6.2 SAR vs. Dot-Product vs. Cosine Similarity as Selection Criteria**

**Figure 2**: Scatter plots showing correlation between each metric and actual transfer performance

- (a) Cosine similarity vs. performance (low ρ)
- (b) Dot-product (DTVG) vs. performance (moderate ρ)
- (c) SAR vs. performance (high ρ)
- Across both prompt tuning AND LoRA

**Table 4**: Transfer performance when using each metric for greedy selection (same greedy algorithm, different metric)

- Key claim: SAR as drop-in replacement for DTVG's metric already improves results

**6.3 Bayesian Optimization Efficiency**

**Figure 3**: Best-found performance vs. number of BO evaluations

- Compare: random search, greedy (DTVG-style), BO with SAR, BO with actual training
- Show convergence curves for different source pool sizes (6, 15, 20)
- Key claim: BO finds better subsets with fewer evaluations, especially at scale

**Figure 4**: Multi-task BO vs. independent BO per target

- Key claim: sharing information across targets improves sample efficiency

**Table 5**: Comparison of search methods with fixed evaluation budget

- Random, greedy, single-task BO, multi-task BO
- Performance at budget = {20, 50, 100} evaluations

**6.4 Selection + Isotropic Merging**

**Table 6**: Ablation showing:

||No Iso|Iso-C|Iso-CTS|
|---|---|---|---|
|All tasks|TA baseline|Iso-C baseline|Iso-CTS baseline|
|DTVG selection|DTVG|DTVG + Iso-C|DTVG + Iso-CTS|
|SpectralBO selection|SpectralBO (no Iso)|SpectralBO + Iso-C|**SpectralBO + Iso-CTS**|

Key claim: Selection and merging are complementary — best results come from combining both.

**Figure 5**: SAR distribution before and after isotropic merging for selected vs. all tasks

- Key claim: Selection + Iso achieves highest alignment

**6.5 Cross-PEFT Transferability**

**Table 7**: Optimal subsets selected under each PEFT method

- Show Jaccard similarity between subsets
- Show rank correlation of subset quality rankings
- Key claim: Substantial overlap but meaningful differences exist

**Figure 6**: Heatmap of SAR matrices computed under LoRA vs. prompt tuning vs. full FT

- Key claim: Subspace structure is partially preserved across methods

**6.6 PEFT-to-Full-Fine-Tuning Transferability**

**Table 8**: Performance when using PEFT-selected subsets for full FT

|Selection source|LoRA FT perf|Full FT perf|
|---|---|---|
|Oracle (exhaustive full FT)|—|best|
|SpectralBO (LoRA-based SAR)|best|???|
|SpectralBO (PT-based SAR)|—|???|
|DTVG (PT-based)|—|???|
|All tasks|—|—|

Key claim: LoRA-based selection is a reliable proxy for full FT (if true) or characterize when it diverges.

**Figure 7**: Multi-fidelity correlation plot

- x-axis: LoRA performance for a subset
- y-axis: Full FT performance for the same subset
- Each point = one (subset, target) pair
- Pearson/Spearman correlation
- Key claim: Strong enough correlation to use PEFT as cheap proxy

**6.7 Scaling Analysis**

**Table 9**: Results across T5-small, T5-base, T5-large

- Key claim: SpectralBO benefits hold across model scales

**Table 10**: Results on LLaMA-3.2-1B and 3B

- Key claim: Generalizes to decoder-only architectures

**6.8 Dynamic vs. Static Selection**

**Figure 8**: Comparison with DTVG's dynamic grouping

- SpectralBO (static selection) vs. DTVG (dynamic per-step grouping)
- Optional: SpectralBO with periodic re-selection during training
- Key claim: Static SAR-guided selection matches or beats dynamic heuristic grouping

---

### Section 7: Analysis and Ablations

**7.1 What Makes a Good Source Task Subset?**

Analyze the properties of subsets selected by SpectralBO:

- Average subset size across targets
- Correlation between subset size and target task properties
- Which source tasks appear most/least frequently
- Visualize the spectral structure of good vs. bad subsets

**7.2 When Does Selection Matter Most?**

Analyze when SpectralBO's advantage over "use all" is largest:

- Very low-resource targets (few-shot)
- Heterogeneous source pools (mixing QA, NLI, sentiment, etc.)
- Large source pools (15+ tasks)
- Smaller models (less capacity to absorb conflicting signals)

**7.3 Failure Cases**

Honestly discuss when SpectralBO doesn't help:

- Very small source pools (6 tasks, exhaustive search is feasible)
- When all source tasks are highly related to target
- When target task is extremely different from all sources

**7.4 Computational Cost Analysis**

|Component|Cost|
|---|---|
|Stage 1: Independent PEFT training|n × C_PEFT (one-time)|
|Stage 2: SAR evaluation per subset|O(SVD) ≈ seconds|
|Stage 2: BO overhead|Negligible|
|Stage 3: Iso merging|O(SVD) ≈ seconds|
|Stage 4: Validation training|k × C_PEFT|
|**Total**|**(n + k) × C_PEFT**|
|Comparison: Exhaustive|**2ⁿ × C_PEFT**|

---

### Section 8: Conclusion

- We introduced SpectralBO, combining geometry-aware task selection (SAR) with Bayesian optimization and isotropic merging
- SAR is a better selection criterion than similarity heuristics
- BO efficiently navigates the combinatorial subset space
- Selection and merging are complementary
- PEFT-based selection [does/partially] transfers to full FT
- Framework generalizes across PEFT methods and model scales

**Limitations**:

- SAR assumes linear subspace structure; may not capture all non-linear interactions
- BO overhead becomes relevant only for very large source pools
- Full FT transferability may be model/domain-dependent

**Future work**:

- Extension to vision and multimodal tasks
- Online/continual task selection as new source tasks become available
- Theoretical analysis of when PEFT and full FT subspaces diverge

---

## 4. Contributions Summary

|#|Contribution|Novelty over DTVG|Novelty over Iso-CTS|
|---|---|---|---|
|1|SAR as task selection criterion|Replaces dot-product heuristic with geometrically principled metric|They don't do selection — they merge all|
|2|Multi-task BO for subset search|Replaces greedy heuristic for NP-hard problem|Not applicable (no selection)|
|3|Selection + Isotropic merging|DTVG has no interference-aware merging|Iso-CTS has no selection|
|4|Cross-PEFT generalization|DTVG = prompt tuning only|Iso-CTS tested on full FT + LoRA, but no selection|
|5|PEFT-to-Full-FT transferability|Not studied in DTVG|Not studied from selection perspective|

---

## 5. Risk Assessment & Mitigation

|Risk|Likelihood|Impact|Mitigation|
|---|---|---|---|
|SAR doesn't correlate with transfer perf for PEFT|Low (strong evidence from Iso-CTS for full FT)|High|Fall back to actual validation performance as BO objective|
|BO doesn't outperform greedy with 6 sources|Medium (search space is small)|Medium|Focus on 15+ source setting; show BO value at scale|
|PEFT selection doesn't transfer to full FT|Medium|Medium|This is itself a finding; characterize divergence|
|Iso merging doesn't help after selection|Low|Medium|Ablation will show; even selection alone is novel|
|Compute budget insufficient for all experiments|Medium|Medium|Prioritize: main table → SAR validation → BO efficiency → FT transfer|

---

## 6. Suggested Experiment Priority

### Phase 1: Foundation (2-3 weeks)

1. Train all source + target tasks independently with prompt tuning AND LoRA (T5-base)
2. Extract task matrices, compute SAR matrices
3. Validate SAR correlation with transfer performance (reproduce Iso-CTS finding for PEFT)
4. Compare SAR vs. dot-product vs. cosine similarity as selection metrics

### Phase 2: Core Method (2-3 weeks)

5. Implement BO pipeline with SAR objective
6. Run SpectralBO for all target tasks, compare with DTVG and baselines
7. Implement Iso-C/Iso-CTS merging on selected subsets
8. Ablation: selection only vs. merging only vs. both

### Phase 3: Scale and Transfer (2-3 weeks)

9. Extend to 15+ source tasks — show BO value at scale
10. Cross-PEFT comparison (LoRA vs. PT vs. adapters)
11. PEFT-to-full-FT transferability experiments
12. Model scaling (T5-small/large, LLaMA)

### Phase 4: Analysis and Writing (2 weeks)

13. Few-shot experiments
14. Detailed analysis, failure cases, computational cost
15. Paper writing

**Estimated total: 8-11 weeks for a complete paper**

---

## 7. Target Venues

|Venue|Fit|Deadline (typical)|
|---|---|---|
|**ACL / EMNLP / NAACL**|Strong — NLP focus, PEFT is hot|Feb / May / Jun|
|**NeurIPS / ICML / ICLR**|Strong — ML methodology + BO|May / Jan / Sep|
|**AAAI**|Good|Aug|

Recommendation: Target a top NLP venue (ACL/EMNLP) given the NLP experimental setup, or a top ML venue (NeurIPS/ICML) if you emphasize the BO + spectral analysis methodology.
