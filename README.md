# Data Analysis Experiments

This repository is a set of experiments I did to test and deepen my own data analysis skills for data science. I took the core topics (statistics, A/B testing, statistical tests and estimation) and worked through each one on real public datasets, from the formula by hand up to the Python code and the final decision.

## Why I made it

Most tutorials show one line of library code and a p-value. I wanted to understand what happens underneath: where each formula comes from, why it works, when it breaks, and how to explain the result to someone who is not technical. So for every concept I calculated it by hand first, then checked that Python gives the same number.

Along the way I found real issues in the data and wrote them down instead of hiding them, for example a sample ratio mismatch in an A/B test and a sampling problem in the Titanic file.

## Why it is good for beginners

If you are new to data science, this repo is written for you:

- **Simple language.** Every term is explained the first time it appears. No advanced maths or Python is needed.
- **Same order for every concept:** theory → formula → small example → manual calculation → code → output and interpretation. You always know what comes next.
- **Small examples first, real data second.** You see the idea on 5 numbers before seeing it on 90,000 rows.
- **Real public datasets,** saved in the repo, so everything runs offline without any account.
- **Links to read more** at the difficult parts, and links to the documentation of every library function used.
- **Honest results.** When a test is not significant, or the data has a problem, the notebook says so and explains why it matters.

Colour guide used in the notebooks: $\color{red}{\text{red}}$ = reject the null hypothesis / warning / wrong reading, $\color{green}{\text{green}}$ = fail to reject / passed check / correct reading. Lines starting with a grey bar (quote blocks) hold the definitions and key ideas.

## Contents

### 1. Statistics (`01_statistics/`)

| # | Notebook | Topics | Dataset |
|---|---|---|---|
| 01 | [Basics, variables and first charts](01_statistics/01_basics_variables_and_charts.ipynb) | Descriptive vs inferential, population and sample, sampling techniques, variable types, measurement scales, frequency tables, histograms, KDE | Restaurant tips |
| 02 | [Central tendency and spread](01_statistics/02_central_tendency_and_spread.ipynb) | Mean, median, mode, variance, standard deviation, why n − 1, percentiles, quartiles, box plots, IQR outliers | Restaurant tips |
| 03 | [Normal distribution and z-score](01_statistics/03_normal_distribution_and_z_score.ipynb) | Bell curve, 68-95-99.7 rule, z-scores, standard normal, standardisation vs normalisation, areas under the curve | Galton heights |
| 04 | [Probability](01_statistics/04_probability.ipynb) | Addition and multiplication rules, conditional probability, Bayes, permutations and combinations | Titanic |
| 05 | [Hypothesis testing and confidence intervals](01_statistics/05_hypothesis_testing_and_confidence_intervals.ipynb) | H₀ and H₁, p-values, α, one vs two tails, type I and II errors, power, z and t intervals | Coin simulation, tips, Galton |
| 06 | [Z-test, t-test and chi-square](01_statistics/06_z_test_t_test_chi_square.ipynb) | One-sample z and t tests, proportion z-test, chi-square goodness of fit | Tips, Titanic |
| 07 | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb) | Covariance, Pearson, Spearman, Simpson's paradox, correlation vs causation | Palmer penguins |
| 08 | [Other distributions and the CLT](01_statistics/08_other_distributions_and_clt.ipynb) | Bernoulli, binomial, Poisson, log-normal, Pareto, Q-Q plots, transformations, central limit theorem | Titanic, horse kicks, tips, Cookie Cats |
| 09 | [ANOVA](01_statistics/09_anova.ipynb) | One-way ANOVA by hand, assumptions, Welch and Kruskal-Wallis, Tukey post-hoc, effect size | Palmer penguins |

### 2. Experimentation (`02_experimentation/`)

| # | Notebook | Topics | Dataset |
|---|---|---|---|
| 01 | [A/B testing, part 1: design and data checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) | Business question, metrics, hypotheses, sample size and power, sample ratio mismatch, outliers, exploration | Cookie Cats mobile game |
| 02 | [A/B testing, part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) | Two-proportion z-test, confidence interval, bootstrap, Mann-Whitney, Bonferroni, business impact, peeking and other mistakes | Cookie Cats mobile game |
| 03 | [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb) | Welch and paired t-tests, chi-square independence, ANOVA, correlation tests, Mann-Whitney U, "which test?" table | Tips, sleep, Titanic, penguins |

### 3. Guesstimates (`03_guesstimates/`)

| # | Notebook | Problems |
|---|---|---|
| 01 | [The method and easy problems](03_guesstimates/01_guesstimates_method_and_easy_problems.ipynb) | Water in a lifetime, tennis balls in a bus, coffee sold in Paris |
| 02 | [Data science problems](03_guesstimates/02_guesstimates_data_science_problems.ipynb) | Smartphone sales, app DAU, searches per second, photo storage, subscription revenue, A/B test duration |

## Datasets

All datasets are small public files saved in [`data/`](data/), so the notebooks run without internet or any login. Sources and licences are listed in [`data/README.md`](data/README.md) and at the top of each notebook.

## How to run

```bash
git clone <repository-url>
cd <repository-folder>
pip install -r requirements.txt
jupyter notebook
```

Open the notebooks in order. Each one runs top to bottom on a fresh kernel, and random seeds are fixed, so you get the same numbers as the saved outputs.

## Questions

If something is unclear, or you think I made a mistake somewhere, feel free to open an [issue](../../issues) and ask. I'm happy to explain any part in more detail.
