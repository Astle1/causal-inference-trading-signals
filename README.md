# Causal Inference in Algorithmic Trading Signals

## Code Availability

The implementation code for this project is intentionally not included in this repository at this stage.  
The work presented here is part of an ongoing research paper, and the codebase is being actively refined as the paper undergoes further review and revision.

This repository is intended to document the research motivation, causal methodology, validation strategy, and empirical findings.  
The complete implementation will be released once the research process is finalized.

---

## Overview

Most algorithmic trading systems rely on correlation-based signals, which often appear statistically strong but fail under regime changes or out-of-sample testing. This project is motivated by the observation that correlation alone does not imply a meaningful or actionable relationship in financial markets.

This work develops a multi-stage causal inference pipeline designed to distinguish true, directed causal effects from spurious correlations in financial time series. Rather than optimizing predictive accuracy, the primary objective is to identify relationships that remain stable under rigorous statistical scrutiny and explicit attempts at refutation.

The central guiding principle of this project is adversarial validation: every discovered relationship is treated as suspect until it survives multiple independent tests designed to invalidate it.

---

## Data and Scope

The analysis uses daily financial data from January 1, 2010 to December 31, 2023. The dataset consists of six large-cap US equities and three sector-level exchange-traded funds representing major market segments.

Daily close prices and trading volumes are used to construct derived features that are more suitable for causal analysis. The long time horizon allows the study to span multiple market regimes, including bull markets, bear markets, and high-volatility periods.

---

## Feature Engineering and Preprocessing

Raw price and volume data are transformed into logarithmic returns, logarithmic trading volume, and rolling annualized volatility. Logarithmic returns are used due to their time-additive properties and their ability to treat upward and downward price movements symmetrically.

Volatility is computed using a rolling window to capture recent risk dynamics, while logarithmic volume is used to stabilize extreme spikes in trading activity. The resulting dataset is standardized to ensure numerical stability and to prevent scale effects from biasing regression estimates.

Stationarity is explicitly enforced using the Augmented Dickey-Fuller test. Non-stationary series are excluded from causal discovery, as failure to enforce stationarity is known to generate spurious causal relationships in time-series models.

---

## Causal Discovery Philosophy

Causal discovery in this project is deliberately conservative. Pairwise Granger causality is used only as a screening mechanism to identify candidate relationships, not as evidence of causation.

Candidate relationships are filtered using strict significance thresholds and structural constraints that reflect domain knowledge. In particular, sector-level market indicators are treated as potential upstream drivers, while individual equities are treated primarily as downstream receivers.

The output of this stage is not a final result, but a hypothesis set that is explicitly assumed to contain false positives.

---

## Validation as an Adversarial Process

The core contribution of this project lies in its validation strategy. Each candidate causal relationship is subjected to multiple independent tests, all of which must agree for the relationship to be retained.

Ordinary Least Squares regression is used as the primary estimator, but only in conjunction with backdoor adjustment to control for market-wide confounders. Sector-level variables are included explicitly to test whether apparent stock-to-stock effects disappear once broader market movement is accounted for.

To assess stability, non-parametric bootstrap analysis is performed using hundreds of resampled datasets. Relationships whose confidence intervals cross zero are rejected, regardless of nominal statistical significance.

Placebo tests are used as a direct attempt to falsify causal claims. By deliberately breaking the temporal structure of the treatment variable and re-estimating the model, an empirical null distribution is constructed. Relationships that remain indistinguishable from random noise under this test are discarded.

Finally, multiple hypothesis testing is addressed using False Discovery Rate control. This step is critical given the large number of candidate relationships and is treated as a necessary condition rather than a formality.

---

## Results Under Scrutiny

After applying all validation filters, the majority of initially plausible relationships are rejected. This outcome is intentional and is treated as evidence of the strictness of the pipeline rather than a limitation.

The surviving relationships exhibit consistent patterns across market regimes, including volatility transmission effects and short-term mean-reversion dynamics. Importantly, several relationships that appear strongly correlated in raw data are explicitly shown to lack causal support once confounding variables are controlled for.

A final, highly selective filtering stage isolates a single relationship that satisfies stricter effect-size and statistical confidence requirements. This relationship remains statistically significant, stable under resampling, and robust to placebo testing, despite contradicting naive correlation-based intuition.

---

## Ablation and Self-Critique

An ablation study is conducted to evaluate the necessity of each component of the pipeline. Removing stationarity testing leads to a sharp increase in false causal discoveries driven by non-stationary price trends.

Removing confounder control results in widespread false positives, particularly among stocks within the same sector, demonstrating that many apparent causal effects are artifacts of shared market exposure.

Eliminating bootstrap or placebo validation significantly increases the false discovery rate, confirming that reliance on p-values alone is insufficient for reliable causal inference in financial time series.

These ablation results are treated as evidence that the final findings are not artifacts of any single modeling choice.

---

## Limitations and Future Directions

The current pipeline relies on linear estimators, which limits its ability to capture non-linear causal effects. Future work will explore more expressive causal models that retain interpretability while allowing for richer functional relationships.

The analysis is conducted on daily data, which may obscure shorter-term causal dynamics. Applying the pipeline to higher-frequency data may reveal transient causal effects that are invisible at daily resolution.

Finally, the causal graph is assumed to be static over the full time horizon. Incorporating rolling windows or regime-aware causal discovery may provide a more realistic representation of evolving market structure.

---

## Summary

This project prioritizes causal validity over predictive performance. The methodology is explicitly designed to challenge its own conclusions through multiple independent validation mechanisms.

Rather than maximizing the number of discovered relationships, the pipeline is optimized to eliminate false positives and retain only those effects that survive systematic attempts at refutation.
