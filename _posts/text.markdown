---
layout: single
title:  "Expectation Maximization Algorithm and Gaussian Mixtures"
classes: wide
use_math: true
date:   2022-11-03 10:31:27 -0400
categories: R
excerpt: "This is a post explaining in gentle terms Gaussian Mixtures and the purpose of Expectation Maximization routines."
---

There are many things in our lives that are strictly positive. For example, how many hours do you spend reading a book? I am certainly it is at least zero. Nobody reads minus two hours of books per day.

This is the essence of the bunching principle: sometimes, supposedly continuous variables exhibit a spike of positive probability concentration at a point. In the above example, one _may or may not_ read in their free time, but if they read, the time spent behaves as a continuous variable.

I believe a straightforward example of example is birth weight _versus_ cigarette smokers. Let us first create the first variable in our dataset: average cigarettes per day.


```r
# Load necessary libraries
library(MASS)     # for negative binomial distribution
library(tidyverse) # tidy data manipulation

# Set seed for reproducibility
set.seed(123)

# Set parameters
n <- 1000          # number of observations
p_nonsmoker <- 0.3 # proportion of non-smokers
mu <- 15           # mean number of cigarettes per day for smokers
size <- 3          # dispersion parameter (smaller values indicate more dispersion)

# Simulate data
nonsmokers <- rep(0, round(n * p_nonsmoker))
smokers <- rnegbin(n - length(nonsmokers), mu = mu, theta = size)
cigarettes <- c(nonsmokers, smokers)

df <- tibble(cigarettes = cigarettes)
```

Here we called the MASS and the tidyverse library to construct our dataset. I generated 1000 observations, where 30 percent is non-smokers (this may be unrealistic, but it is just for the sake of an example).

We require the other parameters to generate the negative binomial distribution.

We can quickly plot the CDF of this variable to show the bunching pattern:
```r
# Calculate empirical CDF
df_cdf <- df %>%
  arrange(cigarettes) %>%
  mutate(cdf = cumsum(cigarettes) / sum(cigarettes),
         ecdf = seq_along(cigarettes) / n)

X11()
# Create CDF plot
ggplot() +
  geom_step(data = df_cdf, aes(x = cigarettes, y = ecdf), color = "blue", size = 1) +
  labs(title = "CDF of Daily Cigarette Consumption",
       x = "Number of Cigarettes",
       y = "Cumulative Probability",
       color = "CDF Type") +
  theme_minimal() +
  theme(legend.position = "bottom")
```

![CDF with a bunching at zero](/assets/images/bunch_CDF.png){:width="400px"}



