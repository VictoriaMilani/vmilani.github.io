---
layout: single
title:  "Quick Start to Doubly Robust DID"
date:   2022-11-03 10:31:27 -0400
categories: R
---
In our first introduction to Causal Inference, we learned about difference-in-differences and propensity score weighting methods to estimate the average treatment effect conditional on covariates.

Covariates are the main cause of counfoundness in causal inference settings, so we should ask: which method should I use? Which one is the best method? Well, actually, you can use both to guarantee unbiased estimators!

I think simulating data is a useful tool to understand the underlying econometric mechanism. Let us create a _very simple_ static labor market. Assume 10k workers, which at some point accumulated experience and are some years old.


```r
library(tidyverse)
set.seed(123)
n <- 10000

# Making Age
X1 <- rnorm(n) # normal for age
X1 <- round((65 - 18) * (X1 - min(X1)) / (max(X1) - min(X1)), 0)
X1 <- X1 + 18

# Making Education
X2 <- rnorm(n) # normal for experience
X2 <- round((20) * (X2 - min(X2)) / (max(X2) - min(X2)), 0) + 4
```

For both age and education, I invoke a standard normal distribution and transform it to generate individuals between 65 and 18 years old, and individuals with on average 13 years of education, following typical US data on education level.

Let us assume the government created a training program "D" to increase the overall welfare of the population. It is simple. Either the worker choose to train (which changes their status to 1) or choose to refrain from training. Let us create the training variable and the outcome variable.

```r
# Generate treatment
ps_true <- plogis(0.5 - 0.1*X1 - 0.1*X2)
D <- rbinom(n, 1, ps_true)
tau <- 0.5

# Generate outcome
Y <- 1 + tau*D + 0.3*X1 + 0.4*X2 + rnorm(n, 0, 0.5)
```

Notice that in this code, D, our treatment variable, is not _totally_ random. First I construct a logistic function based on age and education. Then I draw from a binomial distribution the likelihood of being treated based on the logistic function.

This is the main counfounding factor in quasi-experimental identification! Note that older individuals with more experience will more likely not bother participating in the training program. However, they are also more likely to have higher wages according to our outcome variable specification! The problem is that we do not observe, almost in every case in the real-world, the true relationship between observables in our data and treatment assignment. 

What is the consequence then? What if we calculate the ATE by simply believing we are in a randomized trial and calculating the averages:

```r
r$> mean(Y[D == 1]) - mean(Y[D == 0])
[1] -0.8667278
```

