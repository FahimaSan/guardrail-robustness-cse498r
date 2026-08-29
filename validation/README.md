# Additional Validation Studies

This directory contains additional analyses performed after the main guardrail robustness experiments.

The purpose of these analyses was to examine four questions:

1. **Statistical rigor:** Is the observed difference statistically reliable after accounting for multiple comparisons?
2. **Sample-size sensitivity:** How does the Static-attack result change as the number of samples increases?
3. **Utility measurement:** Does a transport-based metric provide similar information to the original cosine-similarity utility metric?
4. **Model generalization:** Does the observed behavior on Qwen also appear in another target model?

---

## 1. Statistical Rigor

The main comparison was between the No-Defense baseline and PromptGuard across four primary attack types.

| Attack        | No Defense ASR | PromptGuard ASR | p-value | Cohen's h |
| ------------- | -------------: | --------------: | ------: | --------: |
| Static        |          20.8% |           12.8% |  0.0226 |     0.215 |
| Adaptive      |          20.8% |           17.6% |  0.4268 |     0.081 |
| Fragmented    |          10.8% |            7.2% |  0.2109 |     0.126 |
| DSPy-Adaptive |          19.6% |           16.0% |  0.3497 |     0.094 |

The Static attack produced the largest observed reduction in attack success rate, from 20.8% to 12.8%.

Using the conventional significance level of α = 0.05, the Static comparison has p = 0.0226.

However, four comparisons were evaluated. Applying a Bonferroni correction gives:

**α = 0.05 / 4 = 0.0125**

Because 0.0226 > 0.0125, the Static result does not remain statistically significant after correction.

The Cohen's h value of 0.215 indicates a small effect.

### Interpretation

The Static attack provides the strongest observed signal among the four attack types, but the current evidence is not sufficient to claim a statistically significant improvement after multiple-comparison correction.

---

## 2. Sample-Size Sensitivity

The existing 250 Static-attack samples were evaluated at progressively larger sample sizes.

| Sample Size | No Defense ASR | PromptGuard ASR | p-value |
| ----------: | -------------: | --------------: | ------: |
|          50 |          22.0% |           14.0% |   0.436 |
|         100 |          20.0% |           15.0% |   0.457 |
|         150 |          20.7% |           14.7% |   0.226 |
|         200 |          20.0% |           14.0% |   0.143 |
|         250 |          20.8% |           12.8% |   0.023 |

The difference between No Defense and PromptGuard becomes more statistically evident as the sample size increases.

At n = 250, the Static comparison produces the strongest evidence observed in this sensitivity analysis.

This analysis is intended as a sensitivity check rather than a formal claim that 250 samples is the minimum required sample size.

---

## 3. Transport-Based Utility Cross-Check

The original utility analysis used cosine similarity between embeddings.

As an additional check, a transport-based metric using sliced Wasserstein distance was calculated.

The two utility approaches showed moderate agreement rather than complete agreement.

Therefore, the transport-based metric is interpreted as a complementary measurement rather than a direct confirmation of the original utility classification.

The average transport distance was:

* Utility preserved: **0.0343**
* Utility not preserved: **0.0439**

The lower average distance for utility-preserved cases suggests that the transport-based metric captures a related signal, although the classifications do not completely agree.

---

## 4. Preliminary Second-Model Pilot

A small pilot experiment was conducted using Mistral-7B-Instruct to examine whether the Qwen results might extend to another target model.

The pilot used 30 samples.

| Attack   | No Defense ASR | PromptGuard ASR |
| -------- | -------------: | --------------: |
| Static   |          16.7% |           16.7% |
| Adaptive |          16.7% |           20.0% |

The Static result observed on Qwen was not reproduced in this pilot.

Because the pilot is small, these results should not be interpreted as evidence that PromptGuard fails generally on other models. Instead, they indicate that model-specific behavior may warrant further investigation.

---

## Overall Conclusion

The validation studies lead to a more cautious interpretation of the main experiment.

PromptGuard showed its clearest observed reduction for Static attacks on Qwen, decreasing ASR from 20.8% to 12.8%. However, this result did not remain statistically significant after Bonferroni correction for four comparisons, and its effect size was small.

The other attack types showed smaller differences. The transport-based utility analysis provided a related but not fully consistent measurement of utility. Finally, the preliminary Mistral pilot did not reproduce the Static-attack reduction observed on Qwen.

Together, these analyses identify the Static attack on Qwen as the strongest observed signal while also highlighting the need for larger and broader experiments before making claims about statistical significance or cross-model generalization.
