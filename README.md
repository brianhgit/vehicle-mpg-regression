# Vehicle Fuel Efficiency: Multiple Linear Regression Analysis

**Tools:** R, ggplot2, tidyverse, patchwork

---

## Overview

I was curious whether the common intuition that heavier, more powerful vehicles get worse gas mileage actually holds up statistically, and which factors matter most when you account for everything at once. This project builds a multiple linear regression model to quantify those relationships using the UCI MPG dataset, which contains records on 392 vehicles produced between 1970 and 1982.

The central question: do vehicle weight, horsepower, and engine displacement each contribute meaningfully to explaining variation in fuel efficiency, or is there redundancy among predictors?

---

## Dataset

- **Source:** [UCI MPG Dataset via Seaborn](https://raw.githubusercontent.com/mwaskom/seaborn-data/master/mpg.csv)
- **Observations:** 398 total, 392 after removing missing horsepower values
- **Response variable:** Miles per gallon (mpg)
- **Predictors used:** Weight (lbs), Horsepower, Engine Displacement (cu. inches)

| Variable | Mean | Median | Min | Max |
|---|---|---|---|---|
| MPG | 23.45 | 22.75 | 9.00 | 46.60 |
| Weight (lbs) | 2978 | 2804 | 1613 | 5140 |
| Horsepower | 104.5 | 93.5 | 46.0 | 230.0 |
| Displacement | 194.4 | 151.0 | 68.0 | 455.0 |

---

## Methods

### Exploratory Analysis
Scatterplots were produced for each predictor against MPG to assess linearity and direction of association before modeling.

### Model Specification
Two nested models were compared:

- **Full model:** `mpg ~ weight + horsepower + displacement`
- **Reduced model:** `mpg ~ weight + horsepower`

Weight, horsepower, and displacement were selected because they are directly tied to engine performance and vehicle size. Each was expected to negatively associate with fuel efficiency.

### Model Comparison
An **ANOVA partial F-test** was used to evaluate whether adding displacement significantly improved model fit beyond what weight and horsepower already provided.

| Model | Residual DF | RSS | F-statistic | p-value |
|---|---|---|---|---|
| weight + horsepower | 389 | 6993.8 | N/A | N/A |
| weight + horsepower + displacement | 388 | 6980.0 | 0.768 | 0.381 |

The result (p = 0.381) indicates displacement does not significantly improve the model. Given its high correlation with weight and horsepower, it was excluded from the final model due to multicollinearity.

### Model Diagnostics
Four diagnostic plots were examined on the final reduced model:

- **Residuals vs Fitted:** no strong systematic pattern, linearity assumption is reasonable
- **Q-Q Plot:** residuals approximately normal with minor tail deviations
- **Scale-Location:** mild heteroscedasticity at higher fitted values, effect is small
- **Residuals vs Leverage:** max Cook's distance = 0.037 (threshold: 0.5), no influential observations

---

## Results

**Final model:**

```
mpg = 45.64 - 0.00579(weight) - 0.0473(horsepower)
```

| Predictor | Estimate | Std. Error | t-value | p-value |
|---|---|---|---|---|
| Intercept | 45.64 | 0.793 | 57.54 | < 0.001 |
| Weight | -0.00579 | 0.00050 | -11.54 | < 0.001 |
| Horsepower | -0.0473 | 0.0111 | -4.27 | < 0.001 |

- **R² = 0.706**, meaning the model explains approximately 70% of variation in MPG
- Both predictors are significant at p < 0.001

**Interpretation:**
- Holding horsepower constant, each additional pound of vehicle weight is associated with a decrease of approximately 0.0058 MPG
- Holding weight constant, each additional unit of horsepower is associated with a decrease of approximately 0.047 MPG

---

## How to Run

1. Clone the repo or download `analysis.Rmd`
2. Open in RStudio
3. Install required packages if needed:
```r
install.packages(c("tidyverse", "ggplot2", "patchwork"))
```
4. Knit the file or run chunks sequentially. The dataset loads directly from the URL so no local file is needed.

---

## Files

| File | Description |
|---|---|
| `analysis.Rmd` | Full R Markdown with EDA, modeling, and diagnostics |
| `mpg_regression_poster.pdf` | Research poster with visualizations and written analysis |

---

## Limitations

- Slight heteroscedasticity detected at higher fitted values
- Data are observational, so results reflect associations rather than causal relationships
- Dataset covers vehicles from 1970 to 1982 only and findings may not generalize to modern vehicles
