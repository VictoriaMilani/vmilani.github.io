---
layout: single
classes: wide
title: Difference in Differences with Time-Varying Covariates
permalink: /workingpapers/badcontrols
---
<img width="640" alt="image" src="https://github.com/user-attachments/assets/cf0eb1a5-d5eb-46a2-806d-ea33c078295c">

- <a href="https://arxiv.org/pdf/2202.02903" target="_blank">Pre-print manuscript</a>

## Abstract
This paper considers identification and estimation of causal effect parameters from participating in a binary treatment in a difference in differences (DID) setup when the parallel trends assumption holds after conditioning on observed covariates. Relative to existing work in the econometrics literature, we consider the case where the value of covariates can change over time and, potentially, where participating in the treatment can affect the covariates themselves. We propose new empirical strategies in both cases. We also consider two-way fixed effects (TWFE) regressions that include time-varying regressors, which is the most common way that DID identification strategies are implemented under conditional parallel trends. We show that, even in the case with only two time periods, these TWFE regressions are not generally robust to (i) time-varying covariates being affected by the treatment, (ii) treatment effects and/or paths of untreated potential outcomes depending on the level of time-varying covariates in addition to only the change in the covariates over time, (iii) treatment effects and/or paths of untreated potential outcomes depending on time-invariant covariates, (iv) treatment effect heterogeneity with respect to observed covariates, and (v) violations of strong functional form assumptions, both for outcomes over time and the propensity score, that are unlikely to be plausible in most DID applications. Thus, TWFE regressions can deliver misleading estimates of causal effect parameters in a number of empirically relevant cases. We propose both doubly robust estimands and regression adjustment/imputation strategies that are robust to these issues while not being substantially more challenging to implement.

### Suggested Citation
Caetano, Carolina, Brantly Callaway, Stroud Payne, and Hugo Sant'Anna. ``Difference in Differences with Time-Varying Covariates.'', Jun 2024. *Working Paper*.

