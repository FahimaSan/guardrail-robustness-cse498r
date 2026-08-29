# Statistical and Cross-Model Validation of Guardrail Robustness Against Prompt Injection Attacks

## Abstract

This study extends our previous evaluation of guardrail robustness against prompt-injection attacks by conducting additional validation analyses. The original experiments evaluated the effectiveness of PromptGuard against multiple attack strategies using Qwen as the target model. The present study examines the statistical strength of the observed results, sensitivity to sample size, consistency of utility measurements, and preliminary generalization to another target model.

Across the four primary attack types, the Static attack produced the largest observed reduction in attack success rate, decreasing from 20.8% without a defense to 12.8% with PromptGuard. The corresponding p-value was 0.0226 and Cohen's h was 0.215, indicating a small effect. However, after Bonferroni correction for four comparisons, using a corrected significance threshold of 0.0125, the Static result did not remain statistically significant.

A sample-size sensitivity analysis using 50, 100, 150, 200, and 250 samples showed increasing statistical evidence as the sample size increased. A transport-based utility analysis using sliced Wasserstein distance showed moderate agreement with the original cosine-similarity utility measure. Finally, a preliminary 30-sample pilot using Mistral-7B-Instruct did not reproduce the Static-attack reduction observed with Qwen.

These findings provide a more cautious interpretation of the original experiment and highlight the importance of multiple-comparison correction, effect-size reporting, alternative utility measurements, and cross-model validation in guardrail robustness studies.

---

# 1. Introduction

Large language models can be exposed to prompt-injection attacks in which malicious instructions are embedded within otherwise legitimate input. Guardrail systems are designed to detect and block such attacks, but their effectiveness may depend on the attack strategy and the target model.

Our previous study evaluated guardrail robustness using multiple attack strategies, including Static, Adaptive, Fragmented, and DSPy-Adaptive attacks. The main evaluation compared a No-Defense baseline with PromptGuard and measured attack success, utility, and false-positive behavior.

The present study does not replace the original experiment. Instead, it extends it with additional validation analyses designed to answer four questions:

1. Does the observed statistical signal remain after correcting for multiple comparisons?
2. How sensitive is the result to sample size?
3. Does an alternative utility metric produce a similar signal?
4. Does the observed behavior extend to another target model?

---

# 2. Experimental Setup

The primary analysis compares the No-Defense condition with PromptGuard across four attack types:

* Static
* Adaptive
* Fragmented
* DSPy-Adaptive

The main evaluation uses 250 samples per attack condition.

Attack Success Rate (ASR) is used to measure the proportion of attacks that successfully achieve their intended malicious behavior.

Utility is evaluated using the original cosine-similarity-based measure. Additional validation uses a sliced Wasserstein distance as a complementary transport-based metric.

---

# 3. Statistical Rigor

The four primary attack types were evaluated independently using statistical comparisons between the No-Defense and PromptGuard conditions.

| Attack        | No Defense ASR | PromptGuard ASR | p-value | Cohen's h |
| ------------- | -------------: | --------------: | ------: | --------: |
| Static        |          20.8% |           12.8% |  0.0226 |     0.215 |
| Adaptive      |          20.8% |           17.6% |  0.4268 |     0.071 |
| Fragmented    |          10.8% |            7.2% |  0.2109 |     0.124 |
| DSPy-Adaptive |          19.6% |           16.0% |  0.3497 |     0.092 |

The Static attack produced the largest reduction in ASR.

Under the conventional α = 0.05 threshold, the Static result is statistically significant because p = 0.0226.

However, because four comparisons were conducted, a Bonferroni correction was applied:

α_corrected = 0.05 / 4 = 0.0125

The Static p-value of 0.0226 is greater than 0.0125. Therefore, the Static result does not remain statistically significant after multiple-comparison correction.

The Cohen's h value of 0.215 indicates that the magnitude of the Static difference is small.

---

# 4. Sample-Size Sensitivity

To examine the stability of the Static result, the existing dataset was evaluated at sample sizes of 50, 100, 150, 200, and 250.

|   n | No Defense ASR | PromptGuard ASR | p-value |
| --: | -------------: | --------------: | ------: |
|  50 |          22.0% |           14.0% |   0.436 |
| 100 |          20.0% |           15.0% |   0.457 |
| 150 |          20.7% |           14.7% |   0.226 |
| 200 |          20.0% |           14.0% |   0.143 |
| 250 |          20.8% |           12.8% |   0.023 |

The results show that statistical evidence generally becomes stronger as more samples are included. At n = 250, the Static comparison produces the strongest evidence in the sensitivity analysis.

This analysis demonstrates the value of using a larger sample than the initial small pilot. However, it does not establish 250 as a formal minimum required sample size.

---

# 5. Transport-Based Utility Cross-Check

The original study measured utility using cosine similarity between embeddings. To provide an independent perspective, a sliced Wasserstein distance was calculated between source-document and response embedding distributions.

The average transport distance was:

* Utility preserved: 0.0343
* Utility not preserved: 0.0439

The lower average distance in utility-preserved cases indicates that the transport-based metric captures a related signal.

However, the two approaches showed only moderate agreement in their classifications. Therefore, the transport-based analysis should be interpreted as a complementary measurement rather than a direct validation of the original utility metric.

This result demonstrates that the choice of utility metric can influence how response preservation is characterized.

---

# 6. Preliminary Cross-Model Pilot

To investigate whether the Qwen results might generalize to another target model, a small pilot was conducted using Mistral-7B-Instruct.

The pilot contained 30 samples and compared No Defense with PromptGuard.

| Attack   | No Defense ASR | PromptGuard ASR |
| -------- | -------------: | --------------: |
| Static   |          16.7% |           16.7% |
| Adaptive |          16.7% |           20.0% |

The Static attack did not show the reduction observed in the Qwen experiment.

The Adaptive attack showed a small increase in ASR under PromptGuard in this pilot.

Because the pilot contains only 30 samples, these observations should not be interpreted as statistically established evidence of cross-model behavior. Instead, they suggest that further evaluation across target models is necessary.

---

# 7. Discussion

The validation analyses lead to a more cautious interpretation of the original findings.

PromptGuard produced its largest observed improvement against the Static attack on Qwen, reducing ASR from 20.8% to 12.8%. However, the result does not remain statistically significant after correcting for the four primary comparisons.

The effect size was also small, indicating that the magnitude of the observed reduction was limited.

The sample-size sensitivity analysis showed that increasing the number of samples produced stronger statistical evidence for the Static comparison. This supports the use of the larger 250-sample evaluation over the smaller pilot sizes, while not establishing a formal power-based sample-size requirement.

The transport-based utility analysis provided a second perspective on response preservation. Its partial agreement with the original metric suggests that utility measurement may depend on the metric used.

Finally, the preliminary Mistral pilot did not reproduce the Static improvement observed on Qwen. This suggests that guardrail effectiveness may depend on the interaction between the guardrail and the target model, although substantially larger experiments would be required to establish this.

---

# 8. Limitations

Several limitations should be considered.

First, the multiple-comparison correction means that the strongest observed Static result cannot currently be presented as statistically significant across the four primary comparisons.

Second, the sample-size sensitivity analysis evaluates subsets of an existing dataset rather than conducting a prospective statistical power analysis.

Third, the transport-based utility metric does not fully agree with the original cosine-similarity-based classification.

Fourth, the second-model evaluation is only a small pilot with 30 samples and therefore cannot establish cross-model generalization.

These limitations motivate larger and more systematic future experiments.

---

# 9. Conclusion

This study extended the original guardrail robustness evaluation with four validation analyses.

The strongest observed result was obtained for Static attacks against Qwen, where PromptGuard reduced attack success rate from 20.8% to 12.8%. Although this result was below the conventional 0.05 significance threshold, it did not remain statistically significant after Bonferroni correction for four comparisons. The associated effect size was small.

The sample-size analysis showed stronger statistical evidence with increasing sample size. The transport-based utility analysis provided a related but not fully consistent view of utility preservation. Finally, a small Mistral pilot did not reproduce the Static-attack reduction observed on Qwen.

Overall, the validation study supports a cautious conclusion: PromptGuard shows a promising but limited reduction in Static attack success in the Qwen experiment, while stronger claims about statistical significance and cross-model generalization require further experimentation.
