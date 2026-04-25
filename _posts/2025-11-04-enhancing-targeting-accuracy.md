---
layout: post
title: Enhancing Targeting Accuracy Using ML
image: "/posts/classification-title-img.png"
tags: [Customer Targeting, Machine Learning, Classification, Python]
---

This project applies machine learning to improve targeting efficiency for a grocery retailer by reducing mailing costs and increasing return on investment (ROI).

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results](#overview-results)
    - [Growth/Next Steps](#overview-growth)
- [01. Data Overview](#data-overview)
- [02. Modeling Overview](#modelling-overview)
- [03. Logistic Regression](#logreg-title)
- [04. Decision Tree](#clftree-title)
- [05. Random Forest](#rf-title)
- [06. KNN](#knn-title)
- [07. Modeling Summary](#modelling-summary)
- [08. Application](#modelling-application)
- [09. Growth & Next Steps](#growth-next-steps)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

A grocery retailer previously distributed marketing mailers for a “Delivery Club” membership to nearly its entire customer base, resulting in high costs. For future campaigns, the objective is to identify customers with a high likelihood of signing up, enabling a more targeted and cost-efficient outreach strategy.

The goal is to model the probability of customer signup using historical campaign data and customer attributes.

<br>
<br>

### Actions <a name="overview-actions"></a>

The project involved:

- Aggregating customer-level data from transactional and demographic sources
- Evaluating class balance (31% signup vs. 69% non-signup)
- Training and comparing multiple classification models:
  - Logistic Regression
  - Decision Tree
  - Random Forest
  - K-Nearest Neighbors (KNN)
- Evaluating models using accuracy, precision, recall, and F1-score
- Optimizing model performance through preprocessing, feature selection, and threshold tuning

<br>
<br>

### Results <a name="overview-results"></a>

Random Forest was selected as the final model based on consistent performance across evaluation metrics and its ability to provide feature importance insights.

**Classification Accuracy**
- KNN: 0.936  
- Random Forest: 0.935  
- Decision Tree: 0.929  
- Logistic Regression: 0.866  

**Precision**
- KNN: 1.00  
- Random Forest: 0.887  
- Decision Tree: 0.885  
- Logistic Regression: 0.784  

**Recall**
- Random Forest: 0.904  
- Decision Tree: 0.885  
- KNN: 0.762  
- Logistic Regression: 0.69  

**F1 Score**
- Random Forest: 0.895  
- Decision Tree: 0.885  
- KNN: 0.865  
- Logistic Regression: 0.734  

Random Forest provided the best balance between precision and recall while also offering interpretability through feature importance.

<br>
<br>

### Growth/Next Steps <a name="overview-growth"></a>

Potential next steps include:

- Evaluating gradient boosting methods (e.g., XGBoost, LightGBM)
- Expanding feature engineering to capture additional behavioral signals
- Incorporating more recent or real-time data sources

<br>
<br>

___

# Data Overview  <a name="data-overview"></a>

The target variable is *signup_flag*, indicating whether a customer joined the Delivery Club.

Key predictors include customer demographics and behavioral metrics aggregated over the three months prior to the campaign:

| Variable Name | Type | Description |
|---|---|---|
| signup_flag | Dependent | Binary indicator of signup |
| distance_from_store | Independent | Distance from customer to store |
| gender | Independent | Customer-reported gender |
| credit_score | Independent | Most recent credit score |
| total_sales | Independent | Total spend (3 months pre-campaign) |
| total_items | Independent | Total items purchased |
| transaction_count | Independent | Number of transactions |
| product_area_count | Independent | Number of product categories purchased |
| average_basket_value | Independent | Average spend per transaction |

___

# Modeling Overview  <a name="modelling-overview"></a>

The objective is to predict the probability of signup for future campaigns. This enables prioritization of high-likelihood customers, reducing unnecessary outreach costs.

Multiple classification models were evaluated to identify the most effective approach.

___

# Logistic Regression <a name="logreg-title"></a>

Logistic Regression was used as a baseline model. Key preprocessing steps included:

- Removing missing values
- Handling outliers using an interquartile range (IQR) approach
- Encoding categorical variables using one-hot encoding
- Applying feature selection using RFECV

Model performance:
- Accuracy: 0.866  
- Precision: 0.784  
- Recall: 0.69  
- F1 Score: 0.734  

Threshold optimization improved F1-score to 0.78 at a probability threshold of 0.44.

___

# Decision Tree <a name="clftree-title"></a>

Decision Trees required minimal preprocessing and provided interpretable decision rules.

Model performance:
- Accuracy: 0.929  
- Precision: 0.885  
- Recall: 0.885  
- F1 Score: 0.885  

Regularization using max depth improved generalization, with optimal depth identified at 9.

___

# Random Forest <a name="rf-title"></a>

Random Forest improved performance through ensemble learning.

Model performance:
- Accuracy: 0.935  
- Precision: 0.887  
- Recall: 0.904  
- F1 Score: 0.895  

Feature importance analysis identified key predictors:
- distance_from_store
- transaction_count  

Permutation importance confirmed similar rankings, reinforcing model reliability.

___

# K-Nearest Neighbors <a name="knn-title"></a>

KNN required additional preprocessing, including:

- Outlier removal
- Feature scaling using MinMaxScaler
- Feature selection via RFECV

While KNN performed well in accuracy, it showed weaker recall relative to ensemble methods, limiting its suitability for this use case.

___

# Modeling Summary <a name="modelling-summary"></a>

Across all models, Random Forest provided the strongest balance of predictive performance and interpretability. While KNN achieved high accuracy, its lower recall made it less effective for identifying all potential signups.

___

# Application <a name="modelling-application"></a>

The final model can be deployed to:

- Score customers based on predicted signup probability
- Prioritize outreach to high-likelihood segments
- Reduce campaign costs while maintaining effectiveness

___

# Growth & Next Steps <a name="growth-next-steps"></a>

- Test advanced ensemble models (XGBoost, LightGBM)
- Incorporate additional behavioral and temporal features
- Evaluate model performance over time with new campaign data
