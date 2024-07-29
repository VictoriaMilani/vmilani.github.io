---
layout: single
title:  "A Gentle Introduction to the Expectation Maximization Algorithm"
classes: wide
use_math: true
date:   2022-11-03 10:31:27 -0400
categories: R
---

The EM algorithm is a powerful iterative method for finding maximum likelihood estimates in statistical models with "latent variables" or missing data. It was first proposed by [Dempster, Laird, and Rubin, 1977](https://rss.onlinelibrary.wiley.com/doi/abs/10.1111/j.2517-6161.1977.tb01600.x?casa_token=a4oDLWp6MQQAAAAA%3AFERMoGtYF9u5EmMI9VScUGrfJ5VG05FGRKEoQ_5Gkg9VKHDQrclfHMhb0qzGM3GbkQ2RtbVUNQ-t3yyG), and since then it is a common tool in machine learning models.

Actually, it is simple, but tricky. The core idea is to iteratively alternate between two steps until it converges.

- Expectation (E) step: Estimate the missing data given the observed data and current parameter estimates.
- Maximization (M) step: Update the parameters to maximize the likelihood, treating the estimated missing data as if it were observed.

Eventually (hopefully) the algorithm converges. This is particularlly useful for Gaussian mixture models. Say we observe a labor market data with logarithm wages and we suspect it actually is composed of two types of workers: low types, and high types. However, we don't observe the worker type, we only observe the social identifier and their payment.

Let us construct this labor market:

```r
# Generate sample data
set.seed(123)
n <- 10000
true_means <- c(2, 4)
true_sds <- c(0.5, 0.5)
true_weights <- c(0.6, 0.4)

lmarket <- tibble(
  worker_id = 1:n,
  log_wage = c(rnorm(n * true_weights[1], true_means[1], true_sds[1]),
          rnorm(n * true_weights[2], true_means[2], true_sds[2])),
  log_wage = log_wage - min(log_wage) + 1
)

# Checking the log wage histogram
X11()
hist(lmarket$log_wage)
```

Here I generated 10000 workers. Everytime a worker is hired, they draw their payment from a random normal distribution. If they are high types, they draw from $w_i ~ N(4 , 0.5)$, with probability $P_high = 0.4$. On the other hand, low types draw their wages from $w_i ~ N(2 , 0.5)$, with $P_low = 0.6$

The last part of the code plots the log wage histogram of this labor market.
