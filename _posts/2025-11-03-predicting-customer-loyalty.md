---
layout: post
title: Predicting Customer Loyalty Using ML
image: "/posts/regression-title-img.png"
tags: [Customer Loyalty, Machine Learning, Regression, Python]
---

This project applies machine learning to estimate missing customer loyalty scores for a grocery retailer, enabling more complete customer insights and improved targeting.

# Table of contents

- [00. Project Overview](#overview-main)
- [01. Data Overview](#data-overview)
- [02. Modeling Overview](#modelling-overview)
- [03. Linear Regression](#linreg-title)
- [04. Decision Tree](#regtree-title)
- [05. Random Forest](#rf-title)
- [06. Modeling Summary](#modelling-summary)
- [07. Predicting Missing Loyalty Scores](#modelling-predictions)
- [08. Growth & Next Steps](#growth-next-steps)

___

# Project Overview

### Context

A market research consultancy provided customer-level loyalty data for approximately 50% of the retailer’s customer base. The remaining customers lack this metric, creating incomplete visibility into customer behavior.

The objective is to predict *loyalty_score* for untagged customers using existing behavioral and demographic data.

### Actions

- Aggregated customer, transaction, and product-level data
- Built regression models on customers with known loyalty scores
- Evaluated:
  - Linear Regression
  - Decision Tree
  - Random Forest
- Selected the best-performing model for prediction

### Results

**Adjusted R² (Test Set)**
- Random Forest: 0.955  
- Decision Tree: 0.886  
- Linear Regression: 0.754  

**Cross-Validated R²**
- Random Forest: 0.925  
- Decision Tree: 0.871  
- Linear Regression: 0.853  

Random Forest achieved the highest predictive performance and was selected for deployment.

### Key Definition

*Loyalty Score* = proportion of total grocery spend allocated to the retailer.

___

# Data Overview

Target variable: *loyalty_score*

Key features:
- distance_from_store
- gender
- credit_score
- total_sales
- total_items
- transaction_count
- product_area_count
- average_basket_value

___

# Modeling Overview

Goal: predict a continuous target (loyalty_score), making this a regression problem.

___

# Linear Regression

Key steps:
- Remove missing values
- Remove outliers (IQR method)
- One-hot encode categorical variables
- Feature selection via RFECV

Performance:
- R²: 0.78  
- Adjusted R²: 0.754  
- CV R²: 0.853  

___

# Decision Tree

Advantages:
- Minimal preprocessing required
- Strong interpretability

Performance:
- R²: 0.898  
- Adjusted R²: 0.887  
- CV R²: 0.871  

Regularization via max_depth improved generalization.

___

# Random Forest

Ensemble method improving robustness and accuracy.

Performance:
- R²: 0.957  
- Adjusted R²: 0.955  
- CV R²: 0.923  

Key driver:
- distance_from_store (most influential feature)

___

# Modeling Summary

Random Forest provided the strongest predictive performance and was selected for final use.

___

# Predicting Missing Loyalty Scores

Pipeline:
- Apply identical preprocessing steps
- Encode categorical variables
- Generate predictions using trained model

This enables scoring of previously untagged customers.

___

# Growth & Next Steps

- Evaluate gradient boosting models (XGBoost, LightGBM)
- Expand feature engineering
- Tune Random Forest hyperparameters
