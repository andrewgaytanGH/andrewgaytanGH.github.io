---
layout: post
title: Understanding Alcohol Product Relationships Using Association Rule Learning
image: "/posts/association-rules-title-img.png"
tags: [Association Rule Learning, Python]
---

In this project, we use Association Rule Learning to analyze transactional relationships and dependencies between products in the alcohol section of a grocery store.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results](#overview-results)
    - [Growth/Next Steps](#overview-growth)
- [01. Data Overview](#data-overview)
- [02. Apriori Overview](#apriori-overview)
- [03. Data Preparation](#apriori-data-prep)
- [04. Applying The Apriori Algorithm](#apriori-fit)
- [05. Interpreting The Results](#apriori-results)
- [06. Growth & Next Steps](#growth-next-steps)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

The client is evaluating how to reorganize the alcohol section of their stores. Customers report difficulty locating products and often request recommendations for related items. In parallel, the marketing team is interested in launching bundled promotions but requires guidance on which products should be grouped together.

A sample dataset of 3,500 alcohol transactions was provided. The objective of this analysis is to identify patterns and relationships between products that can inform store layout, recommendations, and promotional strategy.

<br>
<br>

### Actions <a name="overview-actions"></a>

Association Rule Learning was applied, specifically the *Apriori* algorithm, to evaluate the strength of relationships between products within transactional data.

The workflow included:
- Installing and importing the **apyori** library
- Loading and restructuring the dataset into a format compatible with the algorithm
- Applying the Apriori algorithm to compute key relationship metrics:
  - Support
  - Confidence
  - Expected Confidence
  - Lift

These metrics were used collectively to identify meaningful product relationships and generate actionable insights.

<br>
<br>

### Results <a name="overview-results"></a>

The strongest relationships were observed between products labeled as "gifts." This suggests that consolidating gift-related items into a dedicated section may improve product discoverability.

There were also consistent relationships between French wines, indicating that organizing products by country may be more effective than grouping strictly by type.

An additional pattern was observed among products labeled "small." While the definition of this label requires clarification, it represents a potential area for further investigation with the client.

A prototype concept was also proposed for a product association search tool. For example, querying products associated with "New Zealand" revealed that these wines are more frequently purchased alongside French and South American wines than Australian wines. This challenges typical regional grouping assumptions and suggests that customer preference may not align with geographic proximity.

<br>
<br>

### Growth/Next Steps <a name="overview-growth"></a>

As this is an exploratory analysis, the next step is to review findings with category managers to validate interpretations and identify practical applications.

Future work includes:
- Extending the analysis across additional product categories
- Applying the methodology to the full product catalog
- Developing a search tool to enable stakeholders to interact with product relationships directly

<br>
<br>

___

# Data Overview  <a name="data-overview"></a>

The dataset consists of 3,500 transactions, where each row represents a transaction and each column represents a product included in that transaction.

```python
# import required Python packages
import pandas as pd
from apyori import apriori

# import the sample data
alcohol_transactions = pd.read_csv("data/sample_data_apriori.csv")
```

Each transaction contains a variable number of products. The dataset is structured in a wide format, which requires transformation before applying the Apriori algorithm.

___

# Apriori Overview  <a name="apriori-overview"></a>

Association Rule Learning identifies relationships between items based on co-occurrence patterns within transactional data.

Common applications include:
- Product placement optimization
- Recommendation systems
- Bundled promotions

The Apriori algorithm evaluates relationships using four key metrics:

### Support
The proportion of transactions that contain both Item A and Item B.

### Confidence
The probability that Item B is purchased given that Item A is purchased.

### Expected Confidence
The baseline probability of purchasing Item B across all transactions.

### Lift
The ratio of Confidence to Expected Confidence. A value greater than 1 indicates a positive association between items.

A key consideration is that high Lift values with low Support should be interpreted cautiously, as they may reflect rare but coincidental patterns.

___

# Data Preparation  <a name="apriori-data-prep"></a>

The Apriori algorithm requires input data in a list-of-lists format.

```python
# drop ID column
alcohol_transactions.drop("transaction_id", axis=1, inplace=True)

# convert to list format
transactions_list = []
for _, row in alcohol_transactions.iterrows():
    transaction = list(row.dropna())
    transactions_list.append(transaction)
```

Each transaction is now represented as a list of products.

___

# Applying The Apriori Algorithm <a name="apriori-fit"></a>

```python
apriori_rules = apriori(
    transactions_list,
    min_support=0.003,
    min_confidence=0.2,
    min_lift=3,
    min_length=2,
    max_length=2
)

apriori_rules = list(apriori_rules)
```

The algorithm returns association rules that meet the specified thresholds.

___

# Interpreting The Results <a name="apriori-results"></a>

The results were converted into a structured DataFrame for analysis:

```python
apriori_rules_df = pd.DataFrame({
    "product1": [list(rule[2][0][0])[0] for rule in apriori_rules],
    "product2": [list(rule[2][0][1])[0] for rule in apriori_rules],
    "support": [rule[1] for rule in apriori_rules],
    "confidence": [rule[2][0][2] for rule in apriori_rules],
    "lift": [rule[2][0][3] for rule in apriori_rules],
})
```

Sorting by Lift highlights the strongest relationships:

```python
apriori_rules_df.sort_values(by="lift", ascending=False, inplace=True)
```

Key observations include:
- Strong clustering of gift-related products
- Regional clustering among wines (e.g., French wines)
- Non-intuitive cross-region relationships (e.g., New Zealand wines)

A filtering approach can also be used to explore targeted relationships:

```python
apriori_rules_df[apriori_rules_df["product1"].str.contains("New Zealand")]
```

This supports exploratory analysis and stakeholder-driven questions.

___

# Growth & Next Steps <a name="growth-next-steps"></a>

This analysis provides a foundation for data-driven product organization and marketing strategy.

Next steps include:
- Validating findings with domain experts
- Expanding the analysis to additional categories
- Building tools to operationalize insights for business users
