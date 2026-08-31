# Amazon Co-Purchasing Analysis

**CS 431: Introduction to Big Data | Dustin Mader**

## Overview

In today's competitive e-commerce landscape, understanding customer purchasing behavior is critical for improving product recommendations and personalizing marketing strategies. This project applied frequent pattern mining to a large-scale Amazon dataset to uncover meaningful co-purchasing relationships between products and generate personalized recommendations for customers.

## Dataset

The dataset was collected by crawling the Amazon website and contains product metadata and review information for **548,552 products** spanning books, music CDs, DVDs, and VHS tapes, collected in the summer of 2006. In total, the data included:

- 1,788,725 product-to-product co-purchase edges
- 7,781,990 customer reviews
- 2,509,699 product category memberships

## Methodology

**Tech stack:** Python, Jupyter Notebook, PySpark, Pandas, NumPy, Matplotlib, and Seaborn.

The raw data arrived as a semi-structured text file, which was parsed and converted into a clean Spark DataFrame with fields including product ID, title, category hierarchy, and co-purchased product IDs. Product and co-purchase IDs were cast to string types to ensure compatibility with the modeling pipeline, and the dataset was split into training (7,248,980 records) and testing (1,812,348 records) sets using an 80/20 split.

**Modeling:** I applied the **FP-Growth algorithm** — a more efficient alternative to Apriori that avoids repeated candidate-set generation by representing the dataset as a compact frequent-pattern tree — to identify frequently co-occurring product sets. I tested multiple combinations of `minSupport` and `minConfidence` thresholds to balance rule precision against coverage, ultimately settling on `minSupport = 0.00001` and `minConfidence = 0.5` to surface meaningful patterns from a highly sparse, long-tail purchase dataset.

## Results

The model extracted frequent itemsets and association rules (evaluated on confidence, lift, and support), then used those rules to generate item-level predictions on both the training and testing sets. These predictions were joined back to individual customers to produce personalized product recommendation lists.

Analyzing the distribution of recommendations per customer showed that most customers received a small number of relevant recommendations (typically 1–5 items), with a long tail of customers receiving more extensive lists — consistent with the natural sparsity of co-purchase behavior in a catalog this large.

## Key Takeaways

- FP-Growth is well-suited for large-scale frequent pattern mining where Apriori's repeated candidate generation would be computationally prohibitive.
- Threshold tuning (`minSupport`/`minConfidence`) has an outsized effect on the practical usefulness of association rules — too loose and the rules are noisy, too strict and coverage collapses.
- Future iterations could incorporate **lift** into the filtering criteria (not just confidence) to prioritize rules that reflect genuine association rather than simple popularity, and further tune thresholds to improve recommendation precision.

---
*Built with PySpark's MLlib FP-Growth implementation on the Amazon co-purchasing dataset.*
