
# Machine Learning

## Feature Engineering

Making data more useful, especially as input to models, like machine learning models.

- **Enrichment:** Deriving more valuable data, values, attributes, etc. related to other data.  For instance
- **Dimension Reduction:** When several fields are highly correlated, combining them with dimension reduction can help models train faster.
- Binning: group values into a set of bins
    - Fixed bins: ranges tend to be consistent, the number of samples in each bin can be uneven.
    - Quantile binning: ranges of bins will vary, but each bin has the same number of samples.  Lends itself to OHE.
- Scaling: also known as _normalization_.  Reduces values to the range from zero to one.
    - Suffers from outliers, which skew the range.
    - can use a random cut forest to deal with outliers.
- Standardization: subtract the mean, divide by the standard deviation.
- Images: may scale dimensions, drop channels, convert to grayscale, crop, scale color ranges, etc.
- Audio: convert to amplitude vs. time
- Convert to tensor: a multi-dimensional array
- Missing Values:
    - Reasons:
        - Missing at random: unrelated to values.  Does not imply a value, may be due to another state.
        - Missing Not at Random: the fact that it's missing is well correlate and indicates a hypothetical value, 
          or is likely due to some other observed value.
        - Missing Completely at random: the fact that it's missing has no implication of its value and is not related to other values.
    - Handling:
        - Set to mean, median, or mode: quick and easy, may not yield great results.
        - Drop the row: easy but can make a big change.
        - Estimate them with a supervised learning algorithm: often has the best results, but is challenging.
        - imputation: replace the missing values.
- Feature Selection: choosing features to keep or remove.  Requires intuition.
    - Principle Component Analysis: PCA, uses unsupervised learning to reduce dimensions, is not intuitive.   Reduces number of features.
    - Removing unimportant features can help an algorithm learn faster.
- Shuffling: in some algorithms, having ordered data can mislead it.  It's better to have data in a random order.
    - Sometimes data appears to be randomly ordered from the start, but is not.
    - Safest bet is to just shuffle it from the start.
- Splitting: split into train and test.
    - Generally 20 to 30 percent is held back for testing.
    - The model must never have been trained on the test data.
    - Splitting lets you know if the model is over-fit.  If the error is much higher on test data than training data, then the 
      model is over-fitting.
- Data Cleaning:
    - First understand: is your data incomplete, irrelevant, are there duplicates, should some of it be combined, split?
      Are there missing values?
    - You can determine with Jupyter notebooks
    - Discrete features are also known as categorical features.
    - **Ordinal:** order matters.
    - **Nominal:** order does not matter.
    - Encoding nominal variables into integers is a bad idea, you should use one-hot encoding.
    - OHE, one-hot encoding: transforms nominal categorical features and creates new binary columns for each observation. 
      Instead of translating nominal values to integers, it splits them into separate binary columns.  
      It leads to problems if there are many alternative categories, we could group, or map the rare columns to one category.
- Text Feature engineering
    - bag of words: splits text by white space, aka tokenizing
    - n-gram size 1: is bag of words
    - n-grams size 2+: produces tokens of two sequential words, and then each individual word.  unigram, bigram, trigram
    - **orthogonal sparse bigram: independent and thinly distributed
- TF-IDF: term frequency, inverse document frequency.
    - common words are less meaningful.
    - vectorized: (#docs, #unique n-grams)
    - pre-process: remove punctuation, lowercase words.  Produce a cartesian product.

  # Judging Models

  - Confusion Matrix: predicted in rows, actual in columns.




## Algorithms

- "K" refers to some constant hyper-parameter
    - "K-fold" cross validation: 
        - if the error rates are approximately the same then the errors are at random.


- MCP: model context protocol

## Types

- Unsupervised:
  - Clustering
  - K-Means
  - Dimension reduction
- Supervised:
  - Classification:
  - Neural Networks, Deep Learning, large language models, gradient descent
  - Random forests
  - K Nearest Neighbors

## Reinforcement Learning

