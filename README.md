# Global Life Expectancy Analysis in R

Exploring how health, education, and socioeconomic conditions relate to life expectancy through regression, predictive modeling, and unsupervised learning.

Developed as a collaborative **STA 141A final project at UC Davis**. **Instructor: Akira Horiguchi.**

## Overview

Why does life expectancy differ across countries, and how well can national indicators predict those differences? This project combines exploratory analysis with multiple linear regression, K-nearest neighbors (KNN), principal component analysis (PCA), and K-means clustering. It examines associations in country-year data; it is not a causal study or an individual health prediction tool.

## Data

The project report describes a WHO life expectancy dataset covering **193 countries, 2000-2015**, with **2,938 country-year observations and 22 variables**. Complete-case filtering leaves **1,649 observations**. Each row represents a country in a particular year, not a separate person or a unique country.

The response is life expectancy at birth. Predictors used across the analyses include adult mortality, BMI, immunization coverage, HIV/AIDS, GDP, income composition of resources, and schooling. Different models use different subsets of these variables.

The data was downloaded from Kaggle's [Life Expectancy (WHO)](https://www.kaggle.com/datasets/kumarajarshi/life-expectancy-who) dataset. The project CSV adapts the column names and order and encodes development status as 0 (Developing) and 1 (Developed); the underlying values and missing entries match the Kaggle data.

The source reads `Life_Expectancy_Data.csv` from the same directory as the Quarto document. **The dataset is not included in this upload.** Use the prepared project CSV, or apply the same column ordering and status encoding to the Kaggle download before running the code: the script renames all 22 columns by position.

## Analysis workflow

1. **Prepare and explore:** remove rows containing missing values, inspect distributions and scatterplots, and summarize correlations.
2. **Build regression models:** compare socioeconomic and health-inclusive models, check variance inflation factors, and inspect residuals, leverage, and influence.
3. **Explore model extensions:** apply stepwise AIC selection, interaction terms, log GDP, and quadratic terms.
4. **Compare prediction:** evaluate regression models with 10-fold cross-validation; test KNN with K = 1, 5, 10, 20, 50, 100, and 200; apply leave-one-out validation to M6.
5. **Explore structure:** perform PCA on eight standardized indicators, inspect the elbow curve, and fit three K-means clusters on PC1 and PC2.

## Reported results

These values come from the supplied final report, not a fresh end-to-end run of this repository.

### Regression and prediction

| Model | In-sample adjusted R-squared | Reported 10-fold error |
| --- | ---: | ---: |
| M4: additive baseline | 0.822 | 13.804 |
| M5: interaction model | 0.836 | 12.779 |
| M6: log GDP and quadratic terms | 0.866 | 10.466 |
| KNN, K = 5 | Not reported | 6.874 |

KNN with K = 5 has the lowest reported error among the compared candidates. The nonlinear regression model offers an interpretable alternative with better reported fit than the additive baseline. Schooling and income composition are positively associated with life expectancy in the exploratory analysis, while adult mortality and HIV/AIDS are negatively associated.

The report labels the error column `CV_MSE`. In the code, regression entries are the square of caret's average cross-validation RMSE, whereas KNN entries are the average of fold MSEs. These aggregations are not identical, so the table should be read as the original project's comparison rather than a precisely standardized benchmark.

### PCA and clustering

The first two principal components account for **58.62%** of predictor variance; the first four account for **81.46%**. K-means on the first two components produces the following profiles:

| Cluster | Country-year observations | Mean life expectancy | Descriptive profile |
| --- | ---: | ---: | --- |
| 1 | 244 | 64.02 years | Lower vaccination coverage |
| 2 | 460 | 61.03 years | Higher HIV/AIDS burden and lower economic indicators |
| 3 | 945 | 74.69 years | Higher schooling, income composition, and vaccination coverage |

These are descriptive groups of observations, not permanent classifications of countries or evidence of intervention effects.

## Files

| File | Purpose |
| --- | --- |
| `F_Wang_Wang_Wu_Zheng_Final_Project.qmd` | Original Quarto source containing R code, interpretation, tables, and plots |
| `README.md` | Project overview, reported results, setup, and team contributions |

The analysis code is preserved as supplied. No model formulas, preprocessing steps, random seeds, or original narrative have been changed for this upload.

## Run the analysis

Requirements: R 4.1 or later (the source uses the native pipe), Quarto, and a LaTeX installation for PDF output. Install the R packages in an R session:

```r
install.packages(c(
  "knitr", "rmarkdown", "tidyverse", "MASS", "broom", "car",
  "ggrepel", "patchwork", "FNN", "caret", "kableExtra", "dplyr"
))
```

Place the original `Life_Expectancy_Data.csv` beside the `.qmd` file. From that directory, render the report:

```sh
quarto render F_Wang_Wang_Wu_Zheng_Final_Project.qmd --to pdf
```

Alternatively, open the `.qmd` in a Quarto-enabled RStudio installation and run its R chunks sequentially. Later chunks depend on earlier objects. Leave-one-out cross-validation can take longer than the other analyses.

## Interpretation and reproducibility notes

- **Observational scope:** country-level associations do not establish causation or individual-level effects.
- **Missing data:** complete-case filtering is applied across the full dataset and can change which country-years are represented.
- **Validation design:** random folds split country-year rows, not countries or time periods. The same country can occur in both training and validation folds, so the reported error does not measure generalization to entirely new countries or future years.
- **KNN preprocessing:** standardization occurs before cross-validation rather than separately within each training fold. K is selected using the same CV results that are reported; there is no separate held-out test set.
- **Model comparison:** regression and KNN use different predictor sets and do not share explicitly fixed fold assignments. Reported differences are not a fully controlled comparison.
- **Source labels:** the README follows the code and numerical tables: M5 is the interaction model, M6 is the nonlinear model, and leave-one-out validation is applied to M6. Some original narrative labels differ.
- **Clustering:** The supplied PDF records a non-convergence warning during the elbow sweep. Cluster findings remain exploratory; the warning should be checked when rerunning.
- **Environment:** package versions are not locked. The repository has not been rendered end-to-end during preparation; recorded results may differ with software versions or random-number behavior.

## Team and contributions

Contributions follow the final report's contribution statement.

| Contributor | Project sections |
| --- | --- |
| Cecelia (Yufei) Wu | Data description, exploratory data analysis, and correlation analysis |
| Hongxuan Wang | Multiple linear regression |
| Jing Wang | Model improvement and predictive comparisons |
| Qingxuan Zheng | Correlation analysis and unsupervised learning |
| All team members | Introduction and conclusion |

## Source materials

Prepared from the team's `F_Wang_Wang_Wu_Zheng_Final_Project.qmd` and its matching final PDF. Numerical summaries above follow the report's displayed tables where its prose and model labels differ. The original report and dataset are not bundled in this upload.

## References

- Kumarajarshi. *Life Expectancy (WHO)* [Dataset]. Kaggle. https://www.kaggle.com/datasets/kumarajarshi/life-expectancy-who

