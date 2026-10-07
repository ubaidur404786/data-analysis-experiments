# Data Analysis Experiments: A/B Testing, Regression, SQL and Guesstimates

Real public datasets, analysed step by step. Every result is first worked out by hand, then checked in Python.

**Python · pandas · NumPy · SciPy · statsmodels · SQL (SQLite) · Matplotlib · seaborn · Jupyter**

This repository has two parts:

1. **[Case studies and results](#part-1-case-studies-and-results)**: five short case studies, the result of each one in plain English, and a link to the notebook with all the details.
2. **[New to statistics? Start here](#part-2-new-to-statistics-start-here)**: a step-by-step path through the basics, with small and simple examples, for anyone who finds data science statistics confusing.

All numbers below come straight from the saved notebook outputs.

---

## Part 1: Case studies and results

| # | Case study | Question | Main result | Notebooks |
| --- | --- | --- | --- | --- |
| 1 | A/B testing | Does moving the first gate in a mobile game from level 30 to level 40 change how many players come back? | Players came back **less** with the gate at level 40 (day-7 retention 19.02% → 18.20%, p = 0.0016). Keep the gate at level 30 | [Part 1](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb), [Part 2](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) |
| 2 | Linear regression | How does a family's food spending grow with its income? | Income explains **83%** of food spending, but food spending grows **slower** than income (elasticity 0.86) | [Linear regression](01_statistics/10_linear_regression.ipynb) |
| 3 | Logistic regression | Who survived the Titanic, and can a model predict it? | Being a man or travelling in third class cut the odds of survival by about **92%**. The model is right **78.9%** of the time vs 59.4% by guessing | [Logistic regression](01_statistics/11_logistic_regression.ipynb) |
| 4 | SQL | Can SQL give the same answers as Python, and when does it silently give wrong ones? | The A/B test rebuilt in SQL gives **exactly the same numbers**. One wrong join made revenue **9 times** too big, with no error | [SQL notebooks](#3-sql-03_sql) |
| 5 | Guesstimates | How do you estimate a number nobody gave you? | 9 problems from easy to hard, e.g. an A/B test on a checkout page needs about **208,000 users per group, about 3 weeks** | [Part 1](04_guesstimates/01_guesstimates_method_and_easy_problems.ipynb), [Part 2](04_guesstimates/02_guesstimates_data_science_problems.ipynb) |

### 1. A/B testing: should the game move its first gate?

**The data:** 90,189 players of the mobile game Cookie Cats. Half got the first gate (a forced pause) at level 30, half at level 40.

**What we found:**

- **The result:** with the gate at level 40, fewer players were still playing after 7 days: **19.02% vs 18.20%**, a drop of 0.82 points (4.3% fewer players).
- **Is it real or luck?** If there were no real difference, a gap this big would appear only 0.16% of the time (p = 0.0016). The true drop is most likely between 0.31 and 1.33 points (95% confidence interval).
- **What it means for the business:** about **8,200 fewer active players per 1 million installs** (somewhere between 3,100 and 13,300).
- **Decision:** keep the gate at level 30.

**How we made sure the result can be trusted:**

| Check | What it means in simple words | Result |
| --- | --- | --- |
| Sample size and power | Do we have enough players to see a small change? | A 1-point change needs 23,664 players per group. We have about 45,000, so yes |
| Sample ratio mismatch | Was the 50/50 split really 50/50? | 49.56% / 50.44% (p = 0.0086): a small problem, reported as a limitation |
| Data cleaning | Any impossible values? | 1 player with 49,854 rounds removed |
| Bootstrap | Re-sample the players 5,000 times: does level 30 still win? | Yes, in 99.8% of the re-samples |
| Multiple metrics | Testing several metrics raises the chance of a false alarm | After Bonferroni correction, only day-7 retention stays significant. Day-1 retention: −0.59 points, p = 0.074, not significant |
| Same test as a regression | Does a different method agree? | Logistic regression: odds ratio 0.947, same p = 0.0016 ([notebook](01_statistics/11_logistic_regression.ipynb)) |

**Common mistakes, measured by simulation:**

- **Peeking** (checking every day and stopping at the first p < 0.05) raised the false alarm rate from **4.8% to 25.2%**.
- **Too-small samples** (3,000 users per group) gave only **13.1%** power, and the "significant" results overstated the true effect about **3 times** (2.5 points instead of 0.8).
- **Bad control:** adding "rounds played" to the regression (it is itself changed by the gate) would wrongly make the effect look **50% bigger**.

Details: [A/B part 1: design and checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) · [A/B part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb)

### 2. Linear regression: income and food spending

**The data:** 235 household budgets from Belgium in 1857 (Engel's data).

- **Income explains 83% of food spending** (R² = 0.83).
- **Engel's law is confirmed:** when income goes up by 1%, food spending goes up by only **0.86%** (95% CI 0.82 to 0.90). So richer families spend a smaller share on food: **70%** for the poorest quarter vs **60%** for the richest.
- **Checking the model:** the spread of the errors grows with income (Breusch-Pagan p = 1.4e-25), so the usual standard errors are too small. The robust ones are **4.6 times larger**. A single household moves the slope from 0.485 to 0.547, while the log model barely changes, so the log model is the safer one.

Details: [Linear regression](01_statistics/10_linear_regression.ipynb)

### 3. Logistic regression: who survived the Titanic?

**The data:** 891 Titanic passengers (sex, class, age, survived or not).

- **The model was built from scratch** (Newton's method in NumPy) and gives the same answer as the statsmodels library.
- **Men** had about **0.08 times** the odds of survival of women, and **third class** passengers about **0.08 times** the odds of first class, with age held fixed.
- **Accuracy: 78.9%**, compared with 59.4% if you always guess "did not survive".
- The same method also re-runs the A/B test above and gets the same answer (odds ratio 0.947, p = 0.0016).

Details: [Logistic regression](01_statistics/11_logistic_regression.ipynb)

### 4. SQL: same answers, and the mistakes that give no error

**The data:** the Titanic and Cookie Cats files loaded into SQLite, and the Chinook music store database (11 tables).

| Case | Result | Notebook |
| --- | --- | --- |
| A/B test in SQL | Unique players, the 49.56% / 50.44% split, the outlier and retention 19.02% vs 18.20%: all the same as in Python, checked automatically with `assert` | [SQL case studies](03_sql/04_sql_case_studies.ipynb) |
| Join fan-out | Joining invoices to their lines before adding up made revenue **9 times too big** (20,848.62 instead of 2,328.60), with no error | [Joins, subqueries and CTEs](03_sql/02_joins_subqueries_and_ctes.ipynb) |
| Silent wrong answers | `NOT IN` with one NULL returned 0 rows instead of 5. `BETWEEN` on text dates missed 2 of 7 invoices. `age <> 30` silently dropped 177 passengers with no age | [SQL basics](03_sql/01_sql_basics_and_query_order.ipynb), [Joins](03_sql/02_joins_subqueries_and_ctes.ipynb), [Case studies](03_sql/04_sql_case_studies.ipynb) |
| Window functions | `NTILE(5)` shows the top 20% of players play **73.8%** of all rounds. `RANK` keeps a tie that `ROW_NUMBER` would silently drop | [Window functions](03_sql/03_window_functions.ipynb) |
| Data quality audit | 7 rules (unique keys, missing links between tables, invoice totals = sum of lines, ranges, NULLs) in one query, all passing | [SQL case studies](03_sql/04_sql_case_studies.ipynb) |

### 5. Guesstimates: estimating a number nobody gave you

Each problem is split into small parts. Every assumption is written down with a reason, the numbers are worked out in Python, and a sensitivity check shows which assumption matters most.

| Problem | Estimate | What matters most |
| --- | --- | --- |
| Water a person drinks in a lifetime | about 60,000 litres | Litres per day |
| Tennis balls that fit in a bus | about 150,000 | Seat space and packing, and ball size (cubed) |
| Coffee cups sold in Paris per day | about 2 million | Visitors and share bought outside |
| Smartphones sold in India per year | about 180 million, after correcting a first try that was 2× too high against reported figures | Years between purchases |
| Daily active users of a food delivery app in one city | about 0.7 million, 0.35 million orders per day | Active days, app share |
| Google searches per second | about 100,000 to 140,000 on average, 2× at peak | Searches per user |
| Photo storage for a social app | about 66 PB per year (copies and extra sizes multiply it by 4.5) | Photo size after compression |
| Revenue of a freemium app | about 4 million € per month | Share of users who pay |
| How long an A/B test must run | about 208,000 users per group, so about 3 weeks | Smallest effect we want to detect |

Details: [Method and easy problems](04_guesstimates/01_guesstimates_method_and_easy_problems.ipynb) · [Data science problems](04_guesstimates/02_guesstimates_data_science_problems.ipynb)

---

## Part 2: New to statistics? Start here

If you are a real beginner and data science statistics feels confusing, read these notebooks **in this order**. Each one:

- starts with **why** we need the idea, with a real-life question,
- explains every new word the first time it appears,
- works the formula out **by hand on 5 to 10 numbers**, then does the same in Python on the full dataset,
- links to the documentation of every function used and to further reading.

| Step | Notebook | What you will learn | One thing you will see |
| --- | --- | --- | --- |
| 1 | [Basics, variables and charts](01_statistics/01_basics_variables_and_charts.ipynb) | Population and sample, types of variables, frequency tables, histograms | The shape of real restaurant bills in a histogram |
| 2 | [Central tendency and spread](01_statistics/02_central_tendency_and_spread.ipynb) | Mean, median, mode, variance, standard deviation, quartiles, box plots, outliers | Why we divide by n − 1 |
| 3 | [Normal distribution and z-score](01_statistics/03_normal_distribution_and_z_score.ipynb) | The bell curve, the 68-95-99.7 rule, z-scores | Who is really taller, a man or a woman, compared with their own group |
| 4 | [Probability](01_statistics/04_probability.ipynb) | Probability rules, conditional probability, Bayes, counting | Titanic: 74.2% of women survived vs 18.9% of men |
| 5 | [Hypothesis testing and confidence intervals](01_statistics/05_hypothesis_testing_and_confidence_intervals.ipynb) | H₀ and H₁, p-values, type I and II errors, power, confidence intervals | What a p-value means, shown with coin flips |
| 6 | [Z-test, t-test and chi-square](01_statistics/06_z_test_t_test_chi_square.ipynb) | One-sample z and t tests, proportion test, chi-square goodness of fit | Each test done by hand, then with SciPy |
| 7 | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb) | Covariance, Pearson, Spearman, correlation vs causation | Simpson's paradox: a negative link overall (r = −0.23) that is positive inside every penguin species (r = 0.39 to 0.65). One typo drops Pearson's r from 0.87 to 0.22 |
| 8 | [Other distributions and the CLT](01_statistics/08_other_distributions_and_clt.ipynb) | Binomial, Poisson, log-normal, Pareto, central limit theorem | The top 20% of players play 74% of all rounds, yet averages of 200 players still look normal |
| 9 | [ANOVA](01_statistics/09_anova.ipynb) | Comparing 3 or more groups, Tukey post-hoc, effect size | Species explains 67% of penguin body mass |
| 10 | [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb) | t-tests (Welch, paired), chi-square, ANOVA, correlation tests, Mann-Whitney U, and a **"which test should I use?"** table | The same data gives p = 0.079 with the wrong test and p = 0.003 with the right one |
| 11 | [Linear regression](01_statistics/10_linear_regression.ipynb) | Least squares by hand, R², checking the errors | Case study 2 above |
| 12 | [Logistic regression](01_statistics/11_logistic_regression.ipynb) | Odds, the sigmoid curve, odds ratios, accuracy | Case study 3 above |
| 13 | [A/B testing, part 1](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) and [part 2](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) | The full A/B test workflow, from the question to the decision | Case study 1 above |
| 14 | [SQL notebooks](#3-sql-03_sql) | Queries, joins, window functions, silent errors | Case study 4 above |
| 15 | [Guesstimates](#4-guesstimates-04_guesstimates) | Estimating with clear assumptions | Case study 5 above |

<!-- Colour guide used in the notebooks: $\color{red}{\text{red}}$ = reject the null hypothesis / warning / wrong reading, $\color{green}{\text{green}}$ = fail to reject / passed check / correct reading. Lines starting with a grey bar (quote blocks) hold the definitions and key ideas. -->

---

## All notebooks

The folders are numbered in reading order.

### 1. Statistics (`01_statistics/`)

| #   | Notebook | Topics | Dataset |
| --- | --- | --- | --- |
| 01  | [Basics, variables and first charts](01_statistics/01_basics_variables_and_charts.ipynb) | Descriptive vs inferential, population and sample, sampling techniques, variable types, measurement scales, frequency tables, histograms, KDE | Restaurant tips |
| 02  | [Central tendency and spread](01_statistics/02_central_tendency_and_spread.ipynb) | Mean, median, mode, variance, standard deviation, why n − 1, percentiles, quartiles, box plots, IQR outliers | Restaurant tips |
| 03  | [Normal distribution and z-score](01_statistics/03_normal_distribution_and_z_score.ipynb) | Bell curve, 68-95-99.7 rule, z-scores, standard normal, standardisation vs normalisation, areas under the curve | Galton heights |
| 04  | [Probability](01_statistics/04_probability.ipynb) | Addition and multiplication rules, conditional probability, Bayes, permutations and combinations | Titanic |
| 05  | [Hypothesis testing and confidence intervals](01_statistics/05_hypothesis_testing_and_confidence_intervals.ipynb) | H₀ and H₁, p-values, α, one vs two tails, type I and II errors, power, z and t intervals | Coin simulation, tips, Galton |
| 06  | [Z-test, t-test and chi-square](01_statistics/06_z_test_t_test_chi_square.ipynb) | One-sample z and t tests, proportion z-test, chi-square goodness of fit | Tips, Titanic |
| 07  | [Covariance and correlation](01_statistics/07_covariance_and_correlation.ipynb) | Covariance, Pearson, Spearman, Simpson's paradox, correlation vs causation | Palmer penguins |
| 08  | [Other distributions and the CLT](01_statistics/08_other_distributions_and_clt.ipynb) | Bernoulli, binomial, Poisson, log-normal, Pareto, Q-Q plots, transformations, central limit theorem | Titanic, horse kicks, tips, Cookie Cats |
| 09  | [ANOVA](01_statistics/09_anova.ipynb) | One-way ANOVA by hand, assumptions, Welch and Kruskal-Wallis, Tukey post-hoc, effect size | Palmer penguins |
| 10  | [Linear regression](01_statistics/10_linear_regression.ipynb) | Least squares by hand, R², t-test for the slope, residual checks, Breusch-Pagan, robust standard errors, log-log elasticity, Cook's distance, confidence vs prediction intervals | Engel household budgets (1857) |
| 11  | [Logistic regression](01_statistics/11_logistic_regression.ipynb) | Odds and log-odds, sigmoid, one-variable model by hand, maximum likelihood with Newton's method from scratch, odds ratios, confusion matrix, pseudo-R², bad controls in A/B tests | Titanic, Cookie Cats |

### 2. Experimentation (`02_experimentation/`)

| #   | Notebook | Topics | Dataset |
| --- | --- | --- | --- |
| 01  | [A/B testing, part 1: design and data checks](02_experimentation/01_ab_testing_part1_design_and_checks.ipynb) | Business question, metrics, hypotheses, sample size and power, sample ratio mismatch, outliers, exploration | Cookie Cats mobile game |
| 02  | [A/B testing, part 2: analysis and decision](02_experimentation/02_ab_testing_part2_analysis_and_decision.ipynb) | Two-proportion z-test, confidence interval, bootstrap, Mann-Whitney, Bonferroni, business impact, peeking and other mistakes | Cookie Cats mobile game |
| 03  | [Other statistical tests](02_experimentation/03_other_statistical_tests.ipynb) | Welch and paired t-tests, chi-square independence, ANOVA, correlation tests, Mann-Whitney U, "which test?" table | Tips, sleep, Titanic, penguins |

### 3. SQL (`03_sql/`)

| #   | Notebook | Topics | Dataset |
| --- | --- | --- | --- |
| 01  | [SQL basics and the order a query runs in](03_sql/01_sql_basics_and_query_order.ipynb) | Why SQLite, SELECT, WHERE, ORDER BY, DISTINCT, NULL, CASE WHEN, GROUP BY, HAVING, logical execution order step by step, integer division | Titanic |
| 02  | [Joins, subqueries and CTEs](03_sql/02_joins_subqueries_and_ctes.ipynb) | Keys, INNER / LEFT / FULL joins and anti-joins by hand, multi-table joins, self join, join fan-out, subqueries, EXISTS, NOT IN and NULL, CTEs | Chinook music store |
| 03  | [Window functions](03_sql/03_window_functions.ipynb) | PARTITION BY, ROW_NUMBER / RANK / DENSE_RANK, top-N per group, running totals, moving averages, window frames, LAG / LEAD, share of total, NTILE | Chinook, Cookie Cats |
| 04  | [SQL case studies](03_sql/04_sql_case_studies.ipynb) | Plan → query → check: A/B test metrics in SQL, data quality audit, date traps, checklist of silent errors | Cookie Cats, Chinook |

SQLite is built into Python, so the SQL notebooks need no database server and no login. The queries also work in PostgreSQL, and the notebooks point out where the two differ.

### 4. Guesstimates (`04_guesstimates/`)

| #   | Notebook | Problems |
| --- | --- | --- |
| 01  | [The method and easy problems](04_guesstimates/01_guesstimates_method_and_easy_problems.ipynb) | Water in a lifetime, tennis balls in a bus, coffee sold in Paris |
| 02  | [Data science problems](04_guesstimates/02_guesstimates_data_science_problems.ipynb) | Smartphone sales, app DAU, searches per second, photo storage, subscription revenue, A/B test duration |

## How the work is done

- **Formula by hand, then code.** Each concept goes theory → formula → small example → manual calculation → Python → interpretation, and the notebook checks that the hand result and the library result match.
- **Problems are reported, not hidden.** For example the sample ratio mismatch in the A/B test, a sampling problem in the Titanic file, unequal spread in the regression, and results that are not significant.
- **Reproducible.** The data is saved in the repo, random seeds are fixed, and every notebook runs top to bottom on a fresh kernel.

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
