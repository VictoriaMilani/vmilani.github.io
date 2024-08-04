---
layout: single
title:  "Expectation Maximization Algorithm and Gaussian Mixtures"
classes: wide
use_math: true
date:   2022-11-03 10:31:27 -0400
categories: R
excerpt: "This is a post explaining in gentle terms Gaussian Mixtures and the purpose of Expectation Maximization routines."
---

Imagine that you are interested in the causal effect of smoking on birth weights. Say you observe a covariate, _mom's education_, and you are willing to control for that.

Okay, that sounds good. Perhaps people with more education are more inclined to not smoke, better healthcare and etc. For simplicity, you could impose a linear model, which makes the expression:

$$y_i = \beta X_i + \gamma Z_i + \varepsilon_i$$

where $y_i$ is the birth weight of baby $i$, $X_i$ is your explanatory variable, number of cigarettes smoked by baby $i$'s mom, and $Z_i$ is the mom's education. $\varepsilon_i$ is a random shock following a normal distribution where $\mathbb{E}[\varepsilon | X, Z] = 0$.

Say you have these variables in R in a pretty neat dataset, so you run the regression and, excited, promptly shows the results to your advisor:

```r
r$> # naive regression
    naive_model <- lm(bw ~ cigs + educ)
    summary(naive_model)

Call:
lm(formula = bw ~ cigs + educ)

Residuals:
    Min      1Q  Median      3Q     Max 
-535.15 -124.99  -11.07  122.14  530.21 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept) 3024.5512    26.5450 113.941   <2e-16 ***
cigs         -39.2510     0.8716 -45.035   <2e-16 ***
educ          -0.7493     1.7104  -0.438    0.661    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 177.8 on 997 degrees of freedom
Multiple R-squared:  0.6788,    Adjusted R-squared:  0.6781 
F-statistic:  1053 on 2 and 997 DF,  p-value: < 2.2e-16
```

_bw_ is the birthweight, measured in grams. You have the average number of cigarettes smoked by the mom in _cigs_, and her education level in _educ_.

Do notice something _weird_ about this regression? Can you say that for every cigarette smoked per day, the baby loses about 40 grams when they are born?

**Probably not.**

This is why it is important to always have economic intuition as your sanity check.

The hint, and this is probably where your advisor would point out, lies on the educ coefficient. _-0.7?_ That seems off, right? It implies babies are worse off as we increase the mom's education.

What is happening here then?

The problem relies on what we do not observe in the data. What if there is a variable, let's call it $\eta$, in which influences not only directly birth weights, but also the propensity to smoke?

![Causal Path](/assets/images/bunch_dag.png)

If that is the case, not only we are probably overestimating the cigarette effect.






2.6 and statistically significant. That looks awesome. You get excited and show this to your advisor. However, as you may well know, they were not _that_ excited.

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

![CDF with a bunching at zero](/assets/images/bunch_CDF.png)

