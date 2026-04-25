---
layout: post
title: The "You Are What You Eat" Customer Segmentation
image: "/posts/clustering-title-img.png"
tags: [Customer Segmentation, Machine Learning, Clustering, Python]
---

In this project, we use k-means clustering to segment the customer base in order to improve business understanding and enhance the relevance of targeted messaging and customer communications.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results](#overview-results)
    - [Growth/Next Steps](#overview-growth)
- [01. Data Overview](#data-overview)
- [02. K-Means](#kmeans-title)
    - [Concept Overview](#kmeans-overview)
    - [Data Preprocessing](#kmeans-preprocessing)
    - [Finding a Good Value for K](#kmeans-k-value)
    - [Model Fitting](#kmeans-model-fitting)
    - [Appending Clusters to Customers](#kmeans-append-clusters)
    - [Segment Profiling](#kmeans-cluster-profiling)
- [03. Application](#kmeans-application)
- [04. Growth & Next Steps](#growth-next-steps)

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

The senior management team at a supermarket chain is seeking to better understand customer purchasing behavior and how lifestyle choices may influence engagement across food categories.

The objective is to use data and machine learning to segment customers based on their interactions with key product categories. These segments can support improved business understanding and more targeted, relevant customer communications.

<br>
<br>

### Actions <a name="overview-actions"></a>

The workflow included:

- Extracting and joining data from the *transactions* and *product_areas* tables
- Aggregating six months of transaction data to the customer level
- Calculating the percentage of spend per product category for each customer
- Applying data preprocessing, including feature scaling
- Using k-means clustering to identify natural groupings within the data
- Determining the optimal number of clusters using the Within Cluster Sum of Squares (WCSS) method
- Assigning cluster labels to customers
- Profiling clusters to identify distinguishing behavioral characteristics

<br>
<br>

### Results <a name="overview-results"></a>

Using WCSS analysis, a three-cluster solution was selected. The distribution of customers across clusters was:

- Cluster 0: 73.6%
- Cluster 1: 11.8%
- Cluster 2: 14.6%

Cluster profiling revealed meaningful behavioral patterns:

- **Cluster 0** showed balanced spending across all product categories, indicating no strong dietary preference
- **Cluster 1** showed high spend in fruit and vegetables, with minimal spend in dairy and meat, suggesting a potential vegan segment
- **Cluster 2** showed moderate spend in dairy, fruit, and vegetables, with minimal meat consumption, suggesting a potential vegetarian segment

This segmentation was labeled the "You Are What You Eat" framework to reflect the behavioral differences across groups.

<br>
<br>

### Growth/Next Steps <a name="overview-growth"></a>

Further analysis could include:

- Segmenting at a more granular level using sub-categories instead of high-level product groups
- Incorporating additional customer attributes such as demographics or location
- Evaluating alternative clustering methods such as hierarchical clustering or DBSCAN

<br>
<br>

___

# Data Overview  <a name="data-overview"></a>

The analysis focuses on customer transactions within food-related product areas.

```python
# import required Python packages
from sklearn.cluster import KMeans
from sklearn.preprocessing import MinMaxScaler
import pandas as pd
import matplotlib.pyplot as plt

# import tables from database
transactions = ...
product_areas = ...

# merge product_area_name on
transactions = pd.merge(transactions, product_areas, how="inner", on="product_area_id")

# drop the non-food category
transactions.drop(
    transactions[transactions["product_area_name"] == "Non-Food"].index,
    inplace=True
)

# aggregate sales at customer level (by product area)
transaction_summary = transactions.groupby(
    ["customer_id", "product_area_name"]
)["sales_cost"].sum().reset_index()

# pivot data to place product areas as columns
transaction_summary_pivot = transactions.pivot_table(
    index="customer_id",
    columns="product_area_name",
    values="sales_cost",
    aggfunc="sum",
    fill_value=0,
    margins=True,
    margins_name="Total"
).rename_axis(None, axis=1)

# transform sales into % sales
transaction_summary_pivot = transaction_summary_pivot.div(
    transaction_summary_pivot["Total"], axis=0
)

# drop total column
data_for_clustering = transaction_summary_pivot.drop(["Total"], axis=1)
```

The resulting dataset represents each customer by the proportion of spend across key product categories.

___

# K-Means <a name="kmeans-title"></a>

### Concept Overview <a name="kmeans-overview"></a>

K-means is an unsupervised learning algorithm that identifies patterns in unlabeled data by grouping observations into clusters based on similarity.

Similarity is typically measured using Euclidean distance in feature space. The number of clusters is defined by the parameter *k*.

The algorithm iteratively:

1. Initializes k centroids
2. Assigns data points to the nearest centroid
3. Recalculates centroid positions based on cluster means
4. Repeats until assignments stabilize

<br>

### Data Preprocessing <a name="kmeans-preprocessing"></a>

Key preprocessing steps include:

- Handling missing values
- Evaluating the impact of outliers
- Applying feature scaling

In this case, missing values are not present due to aggregation, and outliers are less of a concern due to percentage-based features.

Feature scaling is critical, as k-means relies on distance calculations.

```python
scale_norm = MinMaxScaler()
data_for_clustering_scaled = pd.DataFrame(
    scale_norm.fit_transform(data_for_clustering),
    columns=data_for_clustering.columns
)
```

<br>

### Finding a Good Value for K <a name="kmeans-k-value"></a>

The optimal number of clusters is determined using the Within Cluster Sum of Squares (WCSS) method.

```python
k_values = list(range(1, 10))
wcss_list = []

for k in k_values:
    kmeans = KMeans(n_clusters=k, random_state=42)
    kmeans.fit(data_for_clustering_scaled)
    wcss_list.append(kmeans.inertia_)

plt.plot(k_values, wcss_list)
plt.title("WCSS by Number of Clusters")
plt.xlabel("k")
plt.ylabel("WCSS")
plt.show()
```

The "elbow point" in the plot suggests an appropriate value of k = 3.

![alt text](/img/posts/kmeans-optimal-k-value-plot.png "K-Means Optimal k Value Plot")

<br>

### Model Fitting <a name="kmeans-model-fitting"></a>

```python
kmeans = KMeans(n_clusters=3, random_state=42)
kmeans.fit(data_for_clustering_scaled)
```

<br>

### Appending Clusters to Customers <a name="kmeans-append-clusters"></a>

```python
data_for_clustering["cluster"] = kmeans.labels_
```

<br>

### Segment Profiling <a name="kmeans-cluster-profiling"></a>

Cluster sizes:

```python
data_for_clustering["cluster"].value_counts(normalize=True)
```

Cluster characteristics:

```python
cluster_summary = data_for_clustering.groupby("cluster")[
    ["Dairy", "Fruit", "Meat", "Vegetables"]
].mean().reset_index()
```

This profiling highlights distinct behavioral patterns across segments.

___

# Application <a name="kmeans-application"></a>

This segmentation provides a framework for understanding customer behavior at a high level.

The client can use these segments to:

- Tailor marketing campaigns
- Improve product placement
- Monitor changes in customer preferences over time

___

# Growth & Next Steps <a name="growth-next-steps"></a>

- Expand clustering to more granular product categories
- Incorporate additional customer-level features
- Compare results with alternative clustering methods
