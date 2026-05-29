# Heterogeneous Effects of Counter-Attitudinal News on Affective Polarization

A causal machine learning re-analysis of Levy (2021)'s Facebook field experiment, estimating not just the average treatment effect of counter-attitudinal news exposure but **conditional** effects across measures of prior polarization — and documenting where those conditional estimates break down.

> ECO1465 (Causal Inference / ML), University of Toronto. Supervised by Prof. Nazanin Khazra.

## Overview

Levy (2021) ran a large field experiment randomly subscribing Facebook users to pro- or counter-attitudinal news outlets, finding that counter-attitudinal exposure reduced affective polarization. The original analysis identifies a clean average treatment effect (ATE) off the experiment's randomization.

This project takes that same data and asks a different question: *for whom* does the effect hold, and how stable is any heterogeneity we estimate? It does two things. First, it re-estimates the ATE using a battery of modern causal ML estimators as a robustness exercise. Second, it pushes past the ATE to conditional average treatment effects (CATEs) along two pre-treatment measures of polarization — the own-vs-other-party feeling thermometer difference and an empathy difference — and compares what each estimator implies about that heterogeneity.

The short version of the result: the ATE is extremely robust, and the CATE is not. That contrast is the point of the project, not a side note.

## Key findings

**The ATE replicates and is robust across every estimator.** The original ~0.03-unit gap between the counter- and pro-attitudinal arms survives the move from OLS to meta-learners, doubly robust ML, double ML, and generalized random forests. Every causal ML ATE estimate lands inside the 95% confidence interval of the original OLS estimate (point estimates cluster around −0.024 to −0.031). The effect is small but real.

**Prior polarization, not treatment, dominates the prediction.** Tree-based feature importances and the regularized linear models agree that baseline feeling-thermometer and empathy differences are the strongest determinants of post-treatment affective polarization — far more than treatment assignment. This is consistent with the small ATE: most of the variation in the outcome is carried by who people already were.

**The CATE estimates are inconsistent across methods, and that's the headline.** When the same heterogeneity is estimated with S/X-learners, double ML, and GRF, the implied treatment-effect curves along both prior-polarization measures disagree with each other — in places they don't even share a sign. The two conditioning variables are related but only moderately correlated (~0.44), and no method recovers a stable, interpretable heterogeneity profile. Read straight, this is evidence about the current limits of off-the-shelf causal ML for heterogeneity estimation in noisy political-economy settings, where the signal is small relative to baseline variation — not a tuning failure to be papered over.

## Methods

The pipeline moves from transparent linear benchmarks to flexible estimators, so that disagreement among methods can be read against a known baseline:

- **OLS** — baseline, with controls, and with treatment × prior-polarization interactions (linear heterogeneity benchmark)
- **LASSO / Ridge** — regularized linear models (no improvement over OLS with controls; reported as a negative result)
- **Decision tree / Random forest** — feature importance for the determinants of polarization
- **Propensity overlap diagnostics** — logit vs. LightGBM propensity models, used to confirm positivity (a sanity check given the RCT design)
- **Meta-learners** — S-, T-, and X-learners with LightGBM base learners (ATE + CATE)
- **Doubly Robust ML (DRML)** — with OLS, random forest, and LightGBM nuisance models; bootstrapped CIs
- **Double ML (DML)** — linear and non-parametric
- **Generalized Random Forest (GRF) / causal forest** — non-parametric CATE estimation

CATEs are estimated by fixing all covariates at their sample means and sweeping the conditioning variable (thermometer difference, empathy difference) across its observed range.

## Data

The analysis uses the survey data from Levy (2021)'s field experiment: a baseline and endline Facebook survey (≈17,635 endline respondents; ≈16,896 after filtering missing data; 212 variables).

The data is **not redistributed in this repository.** It is publicly available from the AEA replication archive on openICPSR:

- **Replication package:** openICPSR project 121922 — https://doi.org/10.3886/E121922V1
- **Paper:** Levy, Ro'ee (2021), *American Economic Review* 111(3): 831–70 — https://doi.org/10.1257/aer.20191777

To reproduce, download the package from openICPSR and place the relevant survey file(s) where the code expects them (see below).

## Notes and caveats

- Because the source data is from an RCT, identification of the ATE rests on randomization and a minimal set of assumptions; the causal ML layer is primarily a robustness and heterogeneity exercise rather than a new identification strategy.
- Differential attrition between baseline and endline is statistically detectable (large N) but small in magnitude; this is discussed in the write-up.
- The inconsistent CATE estimates are reported as a finding, not resolved. Treat any single method's heterogeneity curve with caution.

## Author

Peter Shi — [github.com/PetershiCA](https://github.com/PetershiCA) · [linkedin.com/in/peter-shi-jhn117](https://linkedin.com/in/peter-shi-jhn117)

## Acknowledgment

Data and experimental design from Ro'ee Levy (2021). This project is an independent re-analysis and is not affiliated with or endorsed by the original author.

