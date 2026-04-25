---
layout: post
title: Assessing Campaign Performance Using Chi-Square Test for Independence
image: "/posts/ab-testing-title-img.png"
tags: [AB Testing, Hypothesis Testing, Chi-Square, Python]
---

This project applies a Chi-Square Test for Independence to evaluate whether two marketing mailers produce different signup rates for a new service.

# Table of contents

- [00. Project Overview](#overview-main)
- [01. Concept Overview](#concept-overview)
- [02. Data Overview & Preparation](#data-overview)
- [03. Applying the Chi-Square Test](#chi-square-application)
- [04. Interpreting Results](#chi-square-results)
- [05. Discussion](#discussion)

___

# Project Overview

### Context

A grocery retailer launched a campaign promoting a paid “Delivery Club.” Customers were randomly assigned to:

- Mailer 1: low-cost format  
- Mailer 2: high-cost format  
- Control: no mailer  

While both mailers outperformed the control group, the key question is whether the higher-cost mailer delivers a statistically significant improvement over the lower-cost alternative.

### Actions

- Filtered campaign data to include only Mailer 1 and Mailer 2 groups  
- Defined hypotheses and significance level (α = 0.05)  
- Constructed a 2×2 contingency table (mailer type × signup flag)  
- Applied the Chi-Square Test for Independence using `scipy`  

### Results

Observed signup rates:
- Mailer 1: 32.8%  
- Mailer 2: 37.8%  

Test statistics:
- Chi-square statistic: 1.94  
- p-value: 0.16  
- Critical value (α = 0.05): 3.84  

Conclusion: Fail to reject the null hypothesis. There is no statistically significant difference in signup rates between the two mailers at the 0.05 level.

___

# Concept Overview

### A/B Testing

A/B testing is a randomized experimental framework used to compare outcomes across groups exposed to different treatments. Results inform data-driven decision-making.

### Hypothesis Testing

Hypothesis testing evaluates whether observed differences are likely due to chance.

- **Null Hypothesis (H₀):** No relationship between variables  
- **Alternative Hypothesis (H₁):** A relationship exists  
- **Significance Level (α):** Threshold for rejecting H₀ (commonly 0.05)

### Chi-Square Test for Independence

This test evaluates whether two categorical variables are independent by comparing observed frequencies to expected frequencies.

It is appropriate when:
- Data is categorical  
- Observations can be summarized in a contingency table  

___

# Data Overview & Preparation

Data source: `campaign_data` table

Key fields:
- `mailer_type` (Mailer1, Mailer2)  
- `signup_flag` (1 = signed up, 0 = did not)  

```python
import pandas as pd
from scipy.stats import chi2_contingency, chi2

campaign_data = ...
campaign_data = campaign_data.loc[campaign_data["mailer_type"] != "Control"]
```

A contingency table is constructed using `pd.crosstab()`.

___

# Applying the Chi-Square Test

```python
observed = pd.crosstab(campaign_data["mailer_type"], campaign_data["signup_flag"]).values
chi2_stat, p_value, dof, expected = chi2_contingency(observed, correction=False)
critical_value = chi2.ppf(1 - 0.05, dof)
```

___

# Interpreting Results

Decision rules:
- If p-value ≤ α → reject H₀  
- If chi-square ≥ critical value → reject H₀  

Observed:
- p-value = 0.16 (> 0.05)  
- chi-square = 1.94 (< 3.84)  

Conclusion: Insufficient evidence to conclude a difference in performance.

___

# Discussion

Although Mailer 2 shows a higher signup rate, the difference is not statistically significant. Increasing marketing spend on higher-cost mailers is therefore not justified based on current evidence.

Additional testing with larger samples may provide more conclusive results.
