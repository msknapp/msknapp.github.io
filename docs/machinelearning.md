---
title: 
weight: 10
description: 
summary: 
lastmod: 2025-08-13
date: 2025-08-13
tags: []
categories: []
series: []
keywords: []
---

- Feature Engineering: Making data more useful, especially as input to models, like machine learning models.
- Enrichment: Deriving more valuable data, values, attributes, etc. related to other data.  For instance
- Dimension Reduction: When several fields are highly correlated, combining them with dimension reduction can help models train faster.
- Data Cleaning:
- First understand: is your data incomplete, irrelevant, are there duplicates, should some of it be combined, split?  Are there missing values?
- You can determine with Jupyter notebooks
- Discrete features are also known as categorical features.
- Ordinal: order matters.
- Nominal: order does not matter.
- Encoding nominal variables into integers is a bad idea, you should use one-hot encoding.
- OHE, one-hot encoding: transforms nominal categorical features and creates new binary columns for each observation. 
  Instead of translating nominal values to integers, it splits them into separate binary columns.  
  It leads to problems if there are many alternative categories, we could group, or map the rare columns to one category.
- Text Feature engineering

# Machine Learning

- Unsupervised:
  - Clustering
  - K-Means
  - Dimension reduction
- Supervised:
  - Classification:
  - Neural Networks, Deep Learning, large language models, gradient descent
  - Random forests
  - K Nearest Neighbors

# Reinforcement Learning