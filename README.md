# A/B Testing & Statistics Case Studies

End-to-end analysis of a 90,189-player mobile-game A/B test, plus statistics and regression case studies on real public data. Every result is worked out by hand first, then checked in Python.

**Python · pandas · NumPy · SciPy · statsmodels · Matplotlib · seaborn · Jupyter**

## Headline result

Moving the first gate in the mobile game Cookie Cats from level 30 to level 40 **lowered day-7 retention from 19.02% to 18.20%** (p = 0.0016). That is about **8,200 fewer active players per 1 million installs**. Decision: **keep the gate at level 30**.

Before trusting that number, the analysis checks power and sample size, flags a sample ratio mismatch, removes an extreme outlier, confirms the result with a bootstrap, and corrects for testing several metrics at once.

## Key results

All numbers below come straight from the saved notebook outputs.

**A/B test on 90,189 players (Cookie Cats mobile game, gate at level 30 vs level 40)**

| What I checked | Result | Notebook |
| --- | --- | --- |
| Day-7 retention (primary metric) | 19.02% vs 18.20%, a drop of 0.82 points (−4.3% relative), z = −3.16, p = 0.0016, 95% CI [−1.33, −0.31] points | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Business impact | about 8,200 fewer players still active on day 7 per 1 million installs (range 3,100 to 13,300) | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Decision | keep the gate at level 30 | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Bootstrap check | gate 30 had higher day-7 retention in 99.8% of bootstrap samples | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Multiple metrics | only day-7 retention stays significant after Bonferroni correction (α = 0.0167) | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Day-1 retention | −0.59 points, p = 0.074, not significant | [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| Sample size and power | detecting a 1-point change in day-7 retention needs 23,664 players per group (80% power, α = 0.05) | [A/B part 1: design and checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) |
| Sample ratio mismatch | split was 49.56% / 50.44%, chi-square p = 0.0086, flagged as a limitation | [A/B part 1: design and checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) |
| Data cleaning | 1 extreme outlier removed (49,854 rounds played) | [A/B part 1: design and checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) |
| Same test as a regression | logistic regression gives odds ratio 0.947 (95% CI [0.916, 0.980]) and the same p = 0.0016. "Controlling" for rounds played (a post-treatment variable) would wrongly inflate the effect by 50% | [Logistic regression](01_statistics/11_logistic_regression.ipynb) |

**Pitfalls, measured by simulation** (notebook: [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb))

- **Peeking:** checking the p-value every day and stopping at p < 0.05 raised the false alarm rate from 4.8% to 25.2%.
- **Too-small samples:** with only 3,000 users per group, power was 13.1%, and the "significant" results overstated the true effect about 3 times (2.5 points instead of 0.8).

**Regression**

| Topic | Result | Notebook |
| --- | --- | --- |
| Linear regression and Engel's law | on 235 household budgets from 1857, income explains 83% of food spending (R² = 0.83). The log-log elasticity is 0.86 (95% CI [0.82, 0.90]): food spending grows slower than income, and the food share falls from 70% to 60% from the poorest to the richest quarter | [Linear regression](01_statistics/10_linear_regression.ipynb) |
| Unequal spread and influential points | Breusch-Pagan p = 1.4e-25. Robust (HC3) standard errors are 4.6 times larger than the classic ones. One household moves the linear slope from 0.485 to 0.547, while the log model barely changes | [Linear regression](01_statistics/10_linear_regression.ipynb) |
| Logistic regression | Titanic survival model with sex, class and age, fitted with Newton's method written from scratch (matches statsmodels). Odds ratio 0.08 for men and 0.08 for third class. Accuracy 78.9% vs a 59.4% baseline | [Logistic regression](01_statistics/11_logistic_regression.ipynb) |

**Statistics**

| Topic | Result | Notebook |
| --- | --- | --- |
| Chi-square test of independence | Titanic survival was 74.2% for women and 18.9% for men (chi-square = 263.1, p = 3.7e-59, Cramér's V = 0.54) | [Probability](01_statistics/04_probability.ipynb), [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb) |
| ANOVA and Tukey post-hoc | species explains 67% of the variation in penguin body mass (F = 341.9, η² = 0.67); Gentoo are about 1,380 g heavier, while Adelie and Chinstrap do not differ (p = 0.92) | [ANOVA](01_statistics/09_anova.ipynb) |
| Simpson's paradox | bill length and bill depth look negatively related overall (r = −0.23) but are positively related inside every species (r = 0.39 to 0.65) | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb) |
| Pearson vs Spearman | a single typo dropped Pearson's r from 0.873 to 0.221, while Spearman's rho barely moved (0.840 to 0.824) | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb) |
| Paired vs independent t-test | the same drug data gave p = 0.079 with the wrong (independent) test and p = 0.003 with the right (paired) one | [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb) |
| Pareto rule and central limit theorem | the top 20% of players played 74% of all game rounds; sample means of size 200 brought the skewness from 6.0 down to 0.52 | [Other distributions and the CLT](01_statistics/08_other_distributions_and_clt.ipynb) |

## How the work is done

- **Formula by hand, then code.** Each concept goes theory → formula → small example → manual calculation → Python → interpretation, and the notebook checks that the hand result and the library result match.
- **Problems are reported, not hidden.** For example the sample ratio mismatch in the A/B test, a sampling problem in the Titanic file, unequal spread in the regression, and results that are not significant.
- **Reproducible.** The data is saved in the repo, random seeds are fixed, and every notebook runs top to bottom on a fresh kernel.

The notebooks are written in simple language, so they also work as a step-by-step guide if you are new to statistics: every term is explained the first time it appears, each idea is shown on 5 numbers before 90,000 rows, and links to further reading and to the documentation of every function are included.

<!-- Colour guide used in the notebooks: $\color{red}{\text{red}}$ = reject the null hypothesis / warning / wrong reading, $\color{green}{\text{green}}$ = fail to reject / passed check / correct reading. Lines starting with a grey bar (quote blocks) hold the definitions and key ideas. -->

## Contents

### 1. Statistics (`01_statistics/`)

| #   | Notebook                                                                                                          | Topics                                                                                                                                        | Dataset                                 |
| --- | ----------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
| 01  | [Basics, variables and first charts](01_statistics/01_basics_variables_and_charts.ipynb)                          | Descriptive vs inferential, population and sample, sampling techniques, variable types, measurement scales, frequency tables, histograms, KDE | Restaurant tips                         |
| 02  | [Central tendency and spread](01_statistics/02_central_tendency_and_spread.ipynb)                                 | Mean, median, mode, variance, standard deviation, why n − 1, percentiles, quartiles, box plots, IQR outliers                                  | Restaurant tips                         |
| 03  | [Normal distribution and z-score](01_statistics/03_normal_distribution_and_z_score.ipynb)                         | Bell curve, 68-95-99.7 rule, z-scores, standard normal, standardisation vs normalisation, areas under the curve                               | Galton heights                          |
| 04  | [Probability](01_statistics/04_probability.ipynb)                                                                 | Addition and multiplication rules, conditional probability, Bayes, permutations and combinations                                              | Titanic                                 |
| 05  | [Hypothesis testing and confidence intervals](01_statistics/05_hypothesis_testing_and_confidence_intervals.ipynb) | H₀ and H₁, p-values, α, one vs two tails, type I and II errors, power, z and t intervals                                                      | Coin simulation, tips, Galton           |
| 06  | [Z-test, t-test and chi-square](01_statistics/06_z_test_t_test_chi_square.ipynb)                                  | One-sample z and t tests, proportion z-test, chi-square goodness of fit                                                                       | Tips, Titanic                           |
| 07  | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb)                                   | Covariance, Pearson, Spearman, Simpson's paradox, correlation vs causation                                                                    | Palmer penguins                         |
| 08  | [Other distributions and the CLT](01_statistics/08_other_distributions_and_clt.ipynb)                             | Bernoulli, binomial, Poisson, log-normal, Pareto, Q-Q plots, transformations, central limit theorem                                           | Titanic, horse kicks, tips, Cookie Cats |
| 09  | [ANOVA](01_statistics/09_anova.ipynb)                                                                             | One-way ANOVA by hand, assumptions, Welch and Kruskal-Wallis, Tukey post-hoc, effect size                                                     | Palmer penguins                         |
| 10  | [Linear regression](01_statistics/10_linear_regression.ipynb)                                                     | Least squares by hand, R², t-test for the slope, residual checks, Breusch-Pagan, robust standard errors, log-log elasticity, Cook's distance, confidence vs prediction intervals | Engel household budgets (1857) |
| 11  | [Logistic regression](01_statistics/11_logistic_regression.ipynb)                                                 | Odds and log-odds, sigmoid, one-variable model by hand, maximum likelihood with Newton's method from scratch, odds ratios, confusion matrix, pseudo-R², bad controls in A/B tests | Titanic, Cookie Cats |

### 2. Experimentation (`02_experimentation/`)

| #   | Notebook                                                                                                         | Topics                                                                                                                       | Dataset                        |
| --- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| 01  | [A/B testing, part 1: design and data checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb)    | Business question, metrics, hypotheses, sample size and power, sample ratio mismatch, outliers, exploration                  | Cookie Cats mobile game        |
| 02  | [A/B testing, part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) | Two-proportion z-test, confidence interval, bootstrap, Mann-Whitney, Bonferroni, business impact, peeking and other mistakes | Cookie Cats mobile game        |
| 03  | [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb)                                   | Welch and paired t-tests, chi-square independence, ANOVA, correlation tests, Mann-Whitney U, "which test?" table             | Tips, sleep, Titanic, penguins |

### 3. Guesstimates (`03_guesstimates/`)

| #   | Notebook                                                                                       | Problems                                                                                               |
| --- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| 01  | [The method and easy problems](03_guesstimates/01_guesstimates_method_and_easy_problems.ipynb) | Water in a lifetime, tennis balls in a bus, coffee sold in Paris                                       |
| 02  | [Data science problems](03_guesstimates/02_guesstimates_data_science_problems.ipynb)           | Smartphone sales, app DAU, searches per second, photo storage, subscription revenue, A/B test duration |

## Datasets

All datasets are small public files saved in [`data/`](data/), so the notebooks run without internet or any login. Sources and licences are listed in [`data/README.md`](data/README.md) and at the top of each notebook.

## How to run

```bash
git clone https://github.com/ubaidur404786/data-analysis-experiments.git
cd data-analysis-experiments
pip install -r requirements.txt
jupyter notebook
```

Open the notebooks in order. Each one runs top to bottom on a fresh kernel, and random seeds are fixed, so you get the same numbers as the saved outputs.

## Questions

If something is unclear, or you think I made a mistake somewhere, feel free to open an [issue](../../issues) and ask. I'm happy to explain any part in more detail.
