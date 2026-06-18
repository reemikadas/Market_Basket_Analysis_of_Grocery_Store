# 🛒 Market Basket Analysis of Grocery Store

[![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python)](https://www.python.org/)
[![mlxtend](https://img.shields.io/badge/mlxtend-Apriori%20%7C%20Association%20Rules-orange)](http://rasbt.github.io/mlxtend/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📌 Overview

This project applies **Market Basket Analysis (MBA)** to transactional grocery store data to uncover hidden purchasing patterns among customers. Using the **Apriori algorithm** and **Association Rule Mining**, the analysis identifies which products are frequently bought together, enabling data-driven decisions around product placement, cross-selling, and promotional bundling.

---

## 🧩 Business Problem

Grocery retailers handle hundreds of SKUs across thousands of daily transactions, making it difficult to intuitively understand co-purchase behavior. Without a systematic approach, stores risk missed upsell opportunities, suboptimal shelf layouts, and generic promotions that fail to resonate with actual buying habits.

---

## 🎯 Business Objective

- Discover frequent itemsets and product associations from customer transaction data
- Quantify the strength of product relationships using **Support**, **Confidence**, and **Lift**
- Identify seasonal and high-frequency products to support inventory and campaign planning
- Deliver actionable recommendations for cross-selling, product bundling, and store layout optimization

---

## 📦 Dataset

### Source
[Kaggle — Groceries Dataset by Heeraldedhia](https://www.kaggle.com/datasets/heeraldedhia/groceries-dataset)

### Dataset Summary

| Attribute | Details |
|---|---|
| Total Records | 38,765 transactions |
| Columns | 3 (`Member_number`, `Date`, `itemDescription`) |
| Distinct Customers | 3,898 |
| Distinct Products | 167 |
| Transaction Period | January 1, 2014 – December 30, 2015 |
| Missing Values | None |

**Columns:**
- `Member_number` — Unique customer identifier
- `Date` — Transaction date
- `itemDescription` — Name of the purchased product

### Dataset Challenges

- The `Date` column was originally stored in `DD-MM-YYYY` string format and required conversion to a proper `datetime` object for temporal analysis.
- Transactions are at the item level (one row per item per customer per date), requiring aggregation into basket-level records before applying the Apriori algorithm.
- The dataset contains no explicit transaction IDs, necessitating the construction of a composite `basket_id` from `customer_id` and `date`.

---

## 🛠️ Tech Stack

| Category | Tools / Libraries |
|---|---|
| Language | Python 3.12 |
| Data Manipulation | pandas, numpy |
| Visualization | matplotlib, seaborn |
| Association Rule Mining | mlxtend (`apriori`, `association_rules`) |
| Environment | Jupyter Notebook |

---

## 🔄 Project Workflow

```
Data Loading → Data Exploration → Data Cleansing → EDA
        → Basket Construction → Apriori (Frequent Itemsets)
                → Association Rule Mining → Rule Filtering & Interpretation
```

1. **Data Collection & Loading** — Loaded CSV from Kaggle; confirmed shape and data types
2. **Data Exploration** — Identified column types, checked for missing values, converted date formats
3. **Data Cleansing** — Renamed columns to lowercase, standardized `item_desc` by stripping extra whitespace
4. **EDA** — Analyzed purchase frequency, distinct customer reach, seasonality, and monthly trends
5. **Basket Construction** — Created a composite `basket_id` (customer × date) and built a binary basket-item matrix (167 columns × unique baskets)
6. **Frequent Itemset Mining** — Applied Apriori with `min_support = 0.002`; extracted 204 multi-item frequent sets
7. **Association Rule Generation** — Generated rules using `confidence` metric with `min_threshold = 0.02`
8. **Rule Filtering & Interpretation** — Filtered to rules with `lift > 1`; sorted by lift to surface the strongest associations

---

## 📊 Exploratory Data Analysis

### Top 5 Products by Purchase Frequency & Distinct Customers

Both purchase frequency (repeat buys) and distinct customer reach point to the same top 5 products, indicating both high demand volume and broad appeal:

| Rank | Product |
|---|---|
| 1 | Whole Milk |
| 2 | Other Vegetables |
| 3 | Rolls/Buns |
| 4 | Soda |
| 5 | Yogurt |

### Most Popular Product by Month (Heatmap)

- **2014**: More variety — rolls/buns dominated January, May, and October; other vegetables appeared in February and August; whole milk led the remaining months.
- **2015**: Whole milk dominated every single month, with purchase counts ranging from 106 (December) to 145 (August) — a significantly stronger lead than in 2014.

### Seasonal Products (Top 5 by Coefficient of Variation)

Products with the highest variability in monthly average purchases — indicating event- or season-driven demand:

| Product | CV Score |
|---|---|
| Rum | 0.676 |
| Finished Products | 0.662 |
| Fish | 0.657 |
| Organic Sausage | 0.630 |
| Specialty Fat | 0.629 |

Key seasonal patterns observed:
- **Finished Products** — Large spike in June; secondary peaks in November–December (possibly holiday-linked)
- **Rum** — Rises in April–May and November (celebrations/festive periods)
- **Fish** — Peaks in August, likely tied to seasonal availability or regional festivals
- **Organic Sausage** — Peaks in June and December (festive consumption)
- **Specialty Fat** — Increases around May and September

### Monthly Transaction Volume

Transaction volume was tracked across all 24 months (Jan 2014 – Dec 2015), with no significant data gaps. The time series spans two full calendar years, providing a stable baseline for seasonality analysis.

---

## 🤖 Machine Learning Model

### Market Basket Analysis (Apriori Algorithm)

This project uses **unsupervised association rule mining** rather than a supervised classification or regression model. The Apriori algorithm identifies all frequent itemsets from the binary basket-item matrix, and association rules are then derived from those itemsets.

**Key Metrics:**

| Metric | Formula | Interpretation |
|---|---|---|
| **Support** | `frq(LHS, RHS) / N` | What percentage of all transactions contain both items |
| **Confidence** | `frq(LHS, RHS) / frq(LHS)` | How likely is RHS to appear given LHS is already in the cart |
| **Lift** | `Confidence(LHS→RHS) / Support(RHS)` | Factor by which co-purchase probability exceeds chance |

**Apriori Parameters:**

| Parameter | Value |
|---|---|
| `min_support` | 0.002 |
| `min_confidence` | 0.02 |
| Lift filter | > 1.0 (positive associations only) |

**Frequent Itemsets Discovered:** 204 multi-item sets

---

## 📈 Association Rules — Lift > 1.0

All rules with lift greater than 1 (positive product associations), sorted by lift:

| Antecedent (LHS) | Consequent (RHS) | Support | Confidence | Lift |
|---|---|---|---|---|
| Curd | Sausage | 0.0029 | 0.0873 | **1.447** |
| Sausage | Curd | 0.0029 | 0.0487 | **1.447** |
| Brown Bread | Canned Beer | 0.0024 | 0.0639 | 1.363 |
| Canned Beer | Brown Bread | 0.0024 | 0.0513 | 1.363 |
| Frozen Vegetables | Sausage | 0.0021 | 0.0740 | 1.226 |
| Sausage | Frozen Vegetables | 0.0021 | 0.0343 | 1.226 |
| Bottled Beer | Sausage | 0.0033 | 0.0737 | 1.222 |
| Sausage | Bottled Beer | 0.0033 | 0.0554 | 1.222 |
| Frankfurter | Other Vegetables | 0.0051 | 0.1363 | 1.116 |
| Other Vegetables | Frankfurter | 0.0051 | 0.0421 | 1.116 |
| Yogurt | Sausage | 0.0057 | 0.0669 | 1.109 |
| Sausage | Yogurt | 0.0057 | 0.0952 | 1.109 |
| Frozen Vegetables | Root Vegetables | 0.0021 | 0.0764 | 1.098 |
| Root Vegetables | Frozen Vegetables | 0.0021 | 0.0307 | 1.098 |
| Rolls/Buns | Chocolate | 0.0028 | 0.0255 | 1.082 |
| Chocolate | Rolls/Buns | 0.0028 | 0.1190 | 1.082 |
| Frozen Meals | Other Vegetables | 0.0021 | 0.1275 | 1.044 |
| Meat | Other Vegetables | 0.0021 | 0.1270 | 1.040 |
| Pastry | Brown Bread | 0.0021 | 0.0388 | 1.030 |
| Brown Bread | Pastry | 0.0021 | 0.0533 | 1.030 |
| Pastry | Sausage | 0.0032 | 0.0620 | 1.028 |
| Sausage | Pastry | 0.0032 | 0.0532 | 1.028 |
| Soda | Sausage | 0.0059 | 0.0613 | 1.015 |
| Sausage | Soda | 0.0059 | 0.0986 | 1.015 |
| Ham | Whole Milk | 0.0027 | 0.1602 | 1.014 |
| Yogurt | Citrus Fruit | 0.0046 | 0.0537 | 1.011 |
| Citrus Fruit | Yogurt | 0.0046 | 0.0868 | 1.011 |
| Root Vegetables | Shopping Bags | 0.0033 | 0.0480 | 1.009 |
| Shopping Bags | Root Vegetables | 0.0033 | 0.0702 | 1.009 |
| Berries | Other Vegetables | 0.0027 | 0.1227 | 1.005 |
| Other Vegetables | Berries | 0.0027 | 0.0219 | 1.005 |
| Fruit/Vegetable Juice | Rolls/Buns | 0.0037 | 0.1100 | 1.000 |
| Rolls/Buns | Fruit/Vegetable Juice | 0.0037 | 0.0340 | 1.000 |

---

## 🏆 Recommended Model

**Apriori with Association Rules (Lift > 1)** is the appropriate and recommended approach for this problem. Since MBA is an unsupervised technique, there is no competing model to compare against — the Apriori algorithm is the standard method for mining frequent itemsets and generating association rules from transactional data.

The parameter choices (`min_support = 0.002`, `min_confidence = 0.02`) were set deliberately low to capture meaningful but less frequent associations in a dataset with 167 products, where most item pairs naturally have very low raw support. Filtering on `lift > 1` then ensures only true positive associations are surfaced.

---

## 💡 Key Findings

1. **Strongest Association — Curd ↔ Sausage (Lift: 1.447):** The highest-lift rule in the dataset. Buying curd increases the likelihood of buying sausage by ~44.7% compared to random chance, and vice versa.

2. **Sausage is a highly connected product:** It appears in the most association rules, linked to curd, bottled beer, frozen vegetables, yogurt, pastry, and soda — making it a prime candidate for bundling and cross-promotional placement.

3. **Brown Bread ↔ Canned Beer (Lift: 1.363):** A strong and somewhat surprising pairing, suggesting a snack/social occasion buying pattern.

4. **Whole Milk dominates transaction volume:** It was the most purchased product by both frequency and distinct customers, and the most popular product in nearly every month of 2015.

5. **Seasonal demand is concentrated in 5 products:** Rum, Finished Products, Fish, Organic Sausage, and Specialty Fat show strong monthly spikes, offering clear inventory planning opportunities.

6. **2015 showed stronger purchasing consolidation:** Whole milk dominated almost every month in 2015, while 2014 showed more variety in monthly top products (rolls/buns, other vegetables also frequently topped).

---

## 💼 Business Impact

| Area | Recommendation |
|---|---|
| **Product Placement** | Place curd and sausage in adjacent shelves or sections; similarly co-locate brown bread and canned beer |
| **Cross-Selling & Bundling** | Create promotional bundles around sausage (with curd, beer, yogurt, or soda) to increase average basket size |
| **Seasonal Inventory Planning** | Stock rum and finished products ahead of June and November; increase fish inventory before August |
| **Targeted Promotions** | Run promotions for seasonal items (rum, organic sausage) in the month before their demand spike |
| **Loyalty Programs** | Use association rules to personalize product recommendations for repeat customers based on past purchase patterns |

---

## 📁 Repository Structure

```
market-basket-analysis-grocery/
│
├── Market_Basket_Analysis_of_Grocery_Store.ipynb   # Main analysis notebook
├── groceries_dataset.csv                            # Raw dataset
└── README.md                                        # Project documentation
```

---

## 👩‍💻 Author

**Reemika Subrata Das**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-reemikadas-blue?logo=linkedin&style=flat)](https://linkedin.com/in/reemikadas)
[![GitHub](https://img.shields.io/badge/GitHub-reemikadas-black?logo=github&style=flat)](https://github.com/reemikadas)
[![Email](https://img.shields.io/badge/Email-das.reemika%40gmail.com-red?logo=gmail&style=flat)](mailto:das.reemika@gmail.com)
