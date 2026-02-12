# Assignment 04 Interpretation Memo

**Student Name:** Daniel Gamboa
**Date:** February 13th, 2026
**Assignment:** REIT Annual Returns and Predictors (Simple Linear Regression)

---

## 1. Regression Overview

You estimated **three** simple OLS regressions of REIT *annual* returns on different predictors:

| Model | Y Variable | X Variable | Interpretation Focus |
|-------|------------|------------|----------------------|
| 1 | ret (annual) | div12m_me | Dividend yield |
| 2 | ret (annual) | prime_rate | Interest rate sensitivity |
| 3 | ret (annual) | ffo_at_reit | FFO to assets (fundamental performance) |

For each model, summarize the key results in the sections below.

---

## 2. Coefficient Comparison (All Three Regressions)

**Model 1: ret ~ div12m_me**
- Intercept (β₀): 0.1082 (SE: 0.006, p-value: 0.000)
- Slope (β₁): -0.0687 (SE: 0.032, p-value: 0.035)
- R²: 0.002 | N: 2527

**Model 2: ret ~ prime_rate**
- Intercept (β₀): 0.1998 (SE: 0.016, p-value: 0.000)
- Slope (β₁): -0.0194 (SE: 0.003, p-value: 0.000)
- R²: 0.016 | N: 2527

**Model 3: ret ~ ffo_at_reit**
- Intercept (β₀): 0.0973 (SE: 0.009, p-value: 0.000)
- Slope (β₁): 0.5770 (SE: 0.567, p-value: 0.309)
- R²: 0.000 | N: 2518

*Note: Model 3 may have fewer observations if ffo_at_reit has missing values; statsmodels drops those rows.*

---

## 3. Slope Interpretation (Economic Units)

**Dividend Yield (div12m_me):**
- A 1 percentage point increase in dividend yield is associated with about a -0.0007 change in annual return (0.07 percentage points).
- The slope is negative, which suggests that a higher dividend yield is barely associated with lower annual returns. The effect is small.

**Prime Loan Rate (prime_rate):**
- A 1 percentage point increase in the year-end prime rate is associated with about a -0.0194 change in annual return (1.94 percentage points).
- The slope is negative and statistically significant, which aligns with the fact that REIT returns decline when rates are higher.

**FFO to Assets (ffo_at_reit):**
- A 1 unit increase in FFO/Assets is associated with about a 0.577 change in annual return.
- The slope is positive but not statistically significant since the data does not prove strong evidence of a relationship.

---

## 4. Statistical Significance

For each slope, at the 5% significance level:
- **div12m_me:** Significant — negative slope with p = 0.035, but economic effects are small.
- **prime_rate:** Significant — negative slope with p < 0.001, strong evidence of correlation.
- **ffo_at_reit:** Not significant — p = 0.309, no reliable association.

**Which predictor has the strongest statistical evidence of a relationship with annual returns?** prime_rate.

---

## 5. Model Fit (R-squared)

Compare R² across the three models:
- prime_rate explains the most variation, although R² is still very low (0.016). In this case, R² values are close to zero, which makes us think that annual returns are mostly driven by other firm and market factors not reflected in single predictors like the ones analyzed.

---

## 6. Omitted Variables

By using only one predictor at a time, we might be omitting:
- Market risk: a higher systematic risk can raise returns.
- Firm size: firm size effects may influence returns and are correlated with yields.
- Momentum: past performance can predict short-term returns.

**Potential bias:** If omitted risk, firm size, and/or momentum is correlated with dividend yield or interest rates, the slope could be biased (either downward or upward) depending on the correlation pattern. Therefore, single-variable estimates should be interpreted with caution.

---

## 7. Summary and Next Steps

**Key Takeaway:**
Prime loan rates show the most negative relationship with REIT annual returns, which is consistent with the sensitivity rate usually found in real estate. Dividend yield has a small negative slope with weak evidence, and FFO/Assets does not prove a statistically reliable connection in this setup.

**What we would do next:**
- Include two or more predictors to compare the results of multiple regressions with the results provided by single-factor regressions.
- Look for OLS assumption violations.
- Examine whether relationships vary by time period or REIT sector (as done in previous assignments with REIT sectors).

---

## Reproducibility Checklist
- [✓] Script runs end-to-end without errors
- [✓] Regression output saved to `Results/regression_div12m_me.txt`, `regression_prime_rate.txt`, `regression_ffo_at_reit.txt`
- [✓] Scatter plots saved to `Results/scatter_div12m_me.png`, `scatter_prime_rate.png`, `scatter_ffo_at_reit.png`
- [✓] Report accurately reflects regression results
- [✓] All interpretations are in economic units (not just statistical jargon)
