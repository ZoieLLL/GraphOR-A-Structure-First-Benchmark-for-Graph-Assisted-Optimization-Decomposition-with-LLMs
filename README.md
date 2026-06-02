# GraphOR: A Structure-First Benchmark for Graph-Assisted Optimization Decomposition with LLMs

## 🎯 Core Innovation

Decomposition is a fundamental strategy for large-scale optimization — by partitioning variables and constraints into semi-independent subproblems, problems that would otherwise be intractable become solvable at scale. However, identifying an effective decomposition structure has traditionally required substantial domain expertise, as it involves recognizing sparse coupling patterns in the variable-constraint topology of the problem.

**The Fundamental Challenge**: Existing OR benchmarks are designed for NL-to-formulation evaluation and lack the structural graph annotations and problem scale that systematic decomposition research requires. Current generation methods treat variable-constraint structure as a by-product and cannot simultaneously control graph topology, problem type diversity, and mathematical validity at scale. This gap has left the application of LLMs to graph-guided optimization decomposition entirely unexplored.

### Our Solution: GraphOR Framework

To bridge this gap, we present **GraphOR**, a **structure-first** method for generating structurally annotated optimization problems suitable for graph-guided decomposition research. Unlike existing approaches that treat graph structure as a by-product, GraphOR fixes the variable-constraint bipartite graph as a blueprint before any content is generated, ensuring annotation integrity by construction.

**Transformative Approach**: GraphOR combines Weisfeiler-Lehman (WL)-controlled topology diversity with a solver-in-the-loop content pipeline and rule-based structural verifier, enabling the construction of one of the first datasets designed for systematic study of graph-guided optimization decomposition. The resulting benchmark of 1,771 instances spanning LP, MILP, and MINLP across three scales establishes a principled foundation for research at the intersection of structured optimization and LLM reasoning.

## 🚀 Key Contributions

### 1. Structure-First Generation Method
A novel generation paradigm that decouples topology from semantics:
- **Blueprint-Driven Design**: The variable-constraint bipartite graph is fixed as a structural scaffold before any content is generated, preventing the LLM from conflating topology with semantics
- **Dual-Track Blueprint Synthesis**: Deterministic graph transformations (scaling, combining, sampling) and LLM-driven topological variations work in tandem to expand the structural pool beyond the seed distribution
- **Weisfeiler-Lehman Similarity Gate**: A gating mechanism that classifies candidates into diversity tiers and enforces threshold-based acceptance, ensuring broad structural coverage without collapsing around the seed distribution

### 2. GraphOR Benchmark Construction
A dedicated benchmark of 1,771 structurally annotated problems:
- **Problem Diversity**: Spans Linear Programming (LP), Mixed-Integer Linear Programming (MILP), and Mixed-Integer Nonlinear Programming (MINLP)
- **Three Scale Brackets**: Small (20–80 variables), Medium (80–200 variables), and Large (200–500 variables) — all exhibiting diverse WL similarity distributions
- **Rich Annotations**: Each instance includes block assignments, coupling constraints, variable-constraint bipartite graph topology, natural language description, and mathematical model
- **Quality Assurance**: All instances undergo solver-based feasibility verification, rule-based structural consistency checks, and ADMM-based decomposability validation

### 3. Systematic LLM Evaluation
A comprehensive evaluation of decomposition capability across five LLMs:
- **Five Diverse Models**: Qwen3-8B, Qwen3-27B, Qwen3-122B, DeepSeek-V4flash, and DeepSeek-R1, spanning three orders of magnitude in parameter count
- **Controlled Ablation**: Each model evaluated with and without graph annotation across all three scales, isolating the effect of structural information
- **Rigorous Protocol**: ADMM convergence and quality loss as decomposition proxy metrics, with sign tests confirming statistical significance

## 🏗 System Architecture

<div align="center">
<img src="figure/figure_overview.jpg" width="1000" alt="GraphOR Architecture">
</div>
<p align="center">
<em>Figure 1: Overview of the GraphOR pipeline. Structure blueprints are extracted from seed problems and diversified via a WL-similarity gate, populated with solver-validated mathematical models and natural language descriptions, and verified through structural and feasibility checks before inclusion in the benchmark.</em>
</p>

## 📊 Benchmark Comparison

<div align="center">
<img src="figure/figure_datasets.jpg" width="800" alt="Dataset Comparison">
</div>
<p align="center">
<em>Figure 2: Comparison of existing OR datasets and benchmarks. Unlike prior datasets targeting NL-to-formulation or end-to-end solving, GraphOR is the first to provide explicit bipartite graph annotations, block partitions, and coupling constraints — the essential ingredients for systematic decomposition research.</em>
</p>

## 🏆 Experimental Results

Extensive experiments validate that LLMs can decompose optimization problems from text alone, and that explicit graph annotation consistently and substantially improves decomposition quality across all models and scales.

### Performance Highlights
- **All LLMs substantially outperform random partition baseline** on both quality loss and ADMM convergence rate across all three scales
- **Graph annotation reduces quality loss by up to 4.8×** on large-scale instances (DeepSeek-R1: QL from 0.58 → 0.12)
- **ΔConv positive for all 15 model×scale cells**, confirming that graph annotation universally improves convergence
- **Improvement is most pronounced at Medium and Large scales**, where structural complexity exceeds what text-based heuristics can capture
- **MINLP benefits most from graph annotation** (ΔQL = −0.62), as the bipartite graph separates structural reasoning from nonlinear algebraic reasoning

<div align="center">
<img src="figure/figure_problem_type.jpg" width="600" alt="Performance by Problem Type">
</div>
<p align="center">
<em>Figure 3: Decomposition performance by problem type (DeepSeek-R1). Graph annotation provides the largest benefit for MINLP instances, where LLMs must simultaneously reason about nonlinear structure, integrality, and coupling topology.</em>
</p>

### Generation Quality

GraphOR is the **only method** that simultaneously maintains high feasibility (≥96.6%) and 100% structural consistency across all three scales:

| Method | Feasibility | Struct. Consistency | Blocks | Coupling Ratio |
|--------|------------|---------------------|--------|----------------|
| Direct | 86–90% | 35–39% | 1.3–1.5 | 0.00 |
| LLMOPT | 49–52% | 52–55% | 1.2–1.3 | 0.00 |
| OptMATH | 52–63% | 100% | 9.5–48.2 | 0.07 |
| ReSocratic | 18–39% | 17–63% | 1.0–1.1 | 0.00 |
| **GraphOR** | **96.6–99.8%** | **100%** | **6.6–45.9** | **0.13–0.14** |

## 🔬 Technical Approach

### Three-Stage Architecture

1. **Structure Blueprint Generation**: Seed problems are parsed into structure signatures (bipartite graph, block partition, coupling constraints, coupling ratio). New blueprints are synthesized through deterministic graph transformations and LLM-driven variations, with WL similarity gating enforcing topological diversity across three tiers.

2. **Content Generation**: A decoupled two-step pipeline prevents structural drift — a solver-validated mathematical model is generated first from the fixed blueprint, then a natural language description is grounded in the validated model with explicit coverage checks ensuring every variable and constraint is referenced.

3. **Structural Verification**: A rule-based verifier ensures alignment between the generated model and the blueprint (variable/constraint set matching, block partition recovery), combined with solver-based feasibility checks. Only instances passing both checks enter the dataset.

### Key Design Decisions

- **Decoupled Generation**: Generating the mathematical model before the natural language description is essential — ablation shows feasibility drops from 98.0% to 20.0% when this decoupling is removed
- **WL Similarity Gate**: Controls diversity within quality constraints rather than maximizing either dimension alone; ablation confirms it anchors diversity within the intended scale
- **ADMM Decomposability Filter**: Only instances where ADMM converges are retained, ensuring that annotated block structures are operationally decomposable

## 💡 Significance

GraphOR establishes one of the first principled benchmarks for research at the intersection of structured optimization and LLM reasoning. The consistent and substantial improvement from graph annotation — observed across all five LLMs and all three scales — demonstrates that structural graph information helps address a key bottleneck for LLM-based decomposition, and that text alone is not sufficient for reliable structural reasoning at scale.

The structure-first generation paradigm, where bipartite graph blueprints are fixed before content generation, provides a generalizable approach for constructing structurally annotated datasets. The benchmark's cross-scale, cross-type design enables fine-grained analysis of how problem structure, scale, and type interact with LLM decomposition capability, opening new directions for graph-guided optimization research.
