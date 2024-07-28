---
layout: single
title:  "Quick Start to Doubly Robust DID"
date:   2022-11-03 10:31:27 -0400
categories: R
---
In our first introduction to Causal Inference, we learned about difference-in-differences and propensity score weighting methods to estimate the average treatment effect conditional on covariates.

Covariates are the main cause of counfoundness in causal inference settings, so we should ask: which method should I use? Which one is the best method? Well, actually, you can use both to guarantee unbiased estimators!

I think simulating data is a useful tool to understand the underlying econometric mechanism. Let us create a _very simple_ static labor market. Assume 10k workers, which at some point accumulated experience and are at some age.


```r
library(tidyverse)
set.seed(123)
n <- 10000
```



