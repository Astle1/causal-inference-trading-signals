# Causal Inference in Algorithmic Trading Signals

## Code Availability

The implementation code for this project is intentionally not included in this repository at this stage.  
The work presented here is part of an ongoing research paper, and the codebase is being actively refined as the paper undergoes further review and revision.

The repository currently serves to document the methodology, experimental design, validation strategy, and key empirical results of the study.  
The complete implementation will be released once the research process is finalized.

---

## Overview

Traditional algorithmic trading models rely on correlation, which can be misleading and lead to false signals. This project develops and implements a rigorous, multi-stage causal inference pipeline to identify robust causal relationships in financial time series, distinguishing them from simple correlations.

The approach moves beyond simple prediction and uses causal inference to uncover directed relationships between major US stocks and sector ETFs. A hybrid framework is employed, where Granger causality is used for candidate discovery and stricter causal validation techniques are applied to filter spurious relationships.

---

## Data and Scope

The analysis uses daily data for six individual stocks (AAPL, MSFT, NVDA, JPM, UNH, XOM) and three sector ETFs (XLK, XLF, XLV) from January 1, 2010 to December 31, 2023. All datasets include daily close price and trading volume.

Primary features engineered from the raw data include logarithmic returns, logarithmic volume, and annualized volatility computed over a 21-day rolling window.

**TABLE 1–9 PLACEHOLDER: RAW DATA SAMPLES**  
**TABLE 10–18 PLACEHOLDER: PROCESSED FEATURE SAMPLES**

---

## Preprocessing and Assumptions

From the raw data, logarithmic returns, logarithmic volume, and annualized volatility were constructed. Rows containing missing values introduced by rolling window calculations were removed, and remaining missing values were forward-filled.

The full dataset was standardized to zero mean and unit variance to ensure numerical stability and prevent large-magnitude features from dominating the analysis. Stationarity of all time-series was verified using the Augmented Dickey-Fuller test, which is a prerequisite for Granger causality analysis.

---

## Causal Discovery

Pairwise Granger causality tests were applied to identify potential time-lagged relationships between all variables. Relationships with p-values below a strict threshold were retained as candidate edges. To control graph complexity, the number of incoming predictors for each target variable was limited to the strongest relationships.

A domain-specific constraint was applied that prioritizes directional influence from sector-level ETFs to individual stocks. The resulting candidate edges were assembled into a directed acyclic graph representing the initial causal hypothesis.

**FIGURE 1 PLACEHOLDER: INFLUENCERS AND RECEIVERS GRAPH**

---

## Validation and Robustness

Each candidate causal relationship was subjected to a four-part validation procedure. Ordinary Least Squares regression with a backdoor adjustment set was used to estimate causal effects while controlling for confounding sector-level variables.

Non-parametric bootstrap analysis with 300 iterations was used to construct 95% confidence intervals, and placebo tests were conducted by permuting treatment vectors to generate empirical null distributions. Multiple hypothesis testing was addressed using the Benjamini-Hochberg procedure to control the False Discovery Rate.

Only relationships that passed all validation criteria were included in the final strict set, resulting in 1,835 robust causal edges.

---

## Results

Analysis of the strict set reveals a hierarchical market structure in which sector ETFs act as causal influencers while individual stocks primarily act as receivers. A recurring pattern of volatility and volume contagion is observed, along with a consistent negative lagged-return dynamic indicative of short-term mean reversion.

After applying an additional filter requiring a larger minimum effect size and stricter false discovery control, a single gold-standard causal relationship was isolated:

XLK_log_return → NVDA_log_return (lag = 1 day)

This relationship is negative and statistically significant, directly contrasting with the strong positive correlation observed between the two assets.

**TABLE 23 PLACEHOLDER: GOLD-STANDARD EDGE STATISTICS**  
**FIGURE 3 PLACEHOLDER: OLS ESTIMATE WITH BOOTSTRAP CONFIDENCE INTERVAL**

---

## Ablation Study

Removing stationarity testing led to a substantial increase in spurious causal discoveries driven by non-stationary price series. Removing backdoor adjustment resulted in widespread false causal links driven by sector-level confounding.

Excluding placebo and bootstrap validation significantly increased the false discovery rate, confirming the necessity of multi-layer validation in large-scale causal discovery.

---

## Conclusion and Future Work

This project presents a comprehensive causal inference pipeline applied to algorithmic trading signals. Thousands of correlation-driven relationships were filtered into a compact and statistically robust causal structure.

Future extensions include the use of non-linear causal estimators, higher-frequency data to capture intraday dynamics, and dynamic causal graphs that adapt to changing market regimes.

