# Robustness of Prompt-Injection Guardrails Against Adaptive and Fragmentation-Based Attacks

CSE498R Directed Research, 
Fahima Shameem , Farjana Rahman Samia  - Supervisor: Mohammad Shifat-E-Rabbi
Department of Electrical and Computer Engineering, North South University


## Overview

This project evaluates whether publicly available prompt-injection guardrail classifiers provide statistically meaningful protection against a range of attack strategies, from direct injection to two original attack methods introduced in this work. The target model is Qwen2.5-7B-Instruct; the guardrails evaluated are ProtectAI's deberta-v3-base-prompt-injection-v2 and deepset's deberta-v3-base-injection.

Live demo: [Attack Replay Console](https://fahimasan.github.io/guardrail-robustness-cse498r/)

![Results chart](results/results_chart.png)

## Key finding

PromptGuard provides a statistically significant reduction in attack success rate only against a direct, unmodified attack. Against three more sophisticated strategies — including both attacks introduced in this work — no statistically significant protection was observed.

| Attack | No Defense ASR | PromptGuard ASR | p-value | Result |
|:---|---:|---:|---:|:---|
| Static | 20.8% | 12.4% | 0.0159 | Significant |
| Adaptive | 20.8% | 17.6% | 0.4268 | Not significant |
| Fragmented | 10.8% | 7.2% | 0.2109 | Not significant |
| DSPy-Adaptive | 19.6% | 16.0% | 0.3497 | Not significant |

n = 250 per condition. 95% Wilson confidence intervals and full utility/false-positive-rate results are reported in the paper.

## Contributions

**Fragmentation Attack.** Splits a malicious instruction into pieces and screens each piece independently before assembly, testing the case where a guardrail screens retrieved documents individually rather than the assembled context — a common deployment pattern in retrieval-augmented systems.

**DSPy-Optimized Adaptive Attack.** Compiles an automated evasion strategy against a target classifier using DSPy, trained on a held-out set disjoint from the evaluation set, rather than relying on a manually written rewrite strategy.

Full methodology in `paper/draft_paper.docx`, Sections 3.5 and 5.

## Repository structure

```
docs/       Interactive results explorer (GitHub Pages source)
notebook/   Full pipeline notebook, executed with outputs preserved
results/    Per-attempt data (CSV) and summary figure
paper/      Draft paper, weekly plans
```

## Reproducing this work

The notebook in `notebook/` is uploaded with every cell's output already saved and can be read directly on GitHub without execution. Re-running it requires a GPU environment (the pipeline was developed on Kaggle) and takes several hours end to end. `results/detailed_attack_logs_final.csv` contains the per-attempt data underlying every reported statistic.

## Methodology summary

- Target model: Qwen2.5-7B-Instruct, 4-bit quantized
- Sample size: 250 malicious payloads, 200 benign payloads, per condition
- Evaluation: LLM-as-judge attack-success grading, semantic-similarity utility scoring, Wilson 95% confidence intervals, Fisher's exact test
- deepset's deberta-v3-base-injection was excluded from the primary comparison after being found to block 100% of benign inputs in this input distribution (Section 6.1 of the paper)

- ## Additional Validation Studies

Following the main guardrail robustness experiments, we conducted four additional analyses to examine the statistical strength, stability, utility measurement, and model generalization of the observed results.

### 1. Statistical Rigor

We evaluated the difference between the No-Defense baseline and PromptGuard across the four primary attack types: Static, Adaptive, Fragmented, and DSPy-Adaptive.

The Static attack showed the largest reduction in attack success rate:

* No Defense: **20.8%**
* PromptGuard: **12.8%**
* p-value: **0.0226**
* Cohen's h: **0.215 (small effect)**

The Static result is below the conventional 0.05 significance level. However, after correcting for four comparisons using the Bonferroni method, the significance threshold becomes **0.0125**, and the Static result does not remain statistically significant.

The remaining attack types showed smaller differences between the No-Defense and PromptGuard conditions.

### 2. Sample-Size Sensitivity

We used the existing 250 samples to examine how the Static attack result changed with increasing sample size.

Sample sizes of **50, 100, 150, 200, and 250** were evaluated.

The analysis showed that the statistical evidence became stronger as the sample size increased, with the strongest evidence observed at **n = 250**.

This analysis provides a sensitivity check on the stability of the observed Static-attack result.

### 3. Transport-Based Utility Cross-Check

The original experiment measured utility using cosine similarity. We additionally evaluated utility using a transport-based metric based on sliced Wasserstein distance.

The two approaches showed moderate agreement rather than complete agreement. Therefore, the transport-based metric is treated as a **complementary utility measure**, rather than as a direct confirmation of the original metric.

The average transport distance was lower when the original utility measure classified the response as utility-preserved, indicating that the two measures capture some related structure while still differing in their classifications.

### 4. Preliminary Second-Model Pilot

To investigate whether the observed behavior was specific to Qwen, we conducted a small pilot using **Mistral-7B-Instruct**.

For the Static attack:

* No Defense: **16.7%**
* PromptGuard: **16.7%**

For the Adaptive attack:

* No Defense: **16.7%**
* PromptGuard: **20.0%**

Unlike the main Qwen experiment, the small Mistral pilot did not reproduce the reduction observed for the Static attack.

Because this pilot used only **30 samples**, it should be interpreted as a preliminary observation rather than evidence of generalization across models.

### Overall Validation Finding

The additional analyses provide a more cautious interpretation of the main results. PromptGuard showed its clearest reduction against Static attacks on Qwen, but this difference did not remain statistically significant after correction for multiple comparisons. The other attack types showed smaller differences, the alternative utility metric showed only moderate agreement with the original utility measure, and the small second-model pilot did not reproduce the Static-attack reduction.

The complete validation data are available in the [`validation/`](validation/) directory.

