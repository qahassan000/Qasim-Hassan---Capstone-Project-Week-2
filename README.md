# RetailGlobe Customer Segmentation
**Using RFM Analysis, K-Means Clustering, ANOVA, and R² to identify who RetailGlobe's customers really are — and what to do about it.**

---

## The Business Problem

RetailGlobe Ltd. is a UK-based online giftware retailer selling to wholesale buyers and direct consumers (the business brief describes 40+ countries; the 2010–2011 dataset covers 11 countries). Their marketing team had been sending the same emails, promotions, and discounts to every customer regardless of their value or behaviour. Acquisition costs were rising and return rates were stagnating.

Marketing Director Emma Watson needed answers to three questions before a board meeting:
1. Who are our VIP customers that we should never lose?
2. Which customers are at risk of leaving?
3. How should we group customers for different marketing campaigns?

This project delivers a data-driven answer to all three using 12 months of real transaction data.

---

## Methodology

The raw dataset of 133,907 transactions was cleaned by removing rows with missing customer IDs, product returns, and duplicates — retaining 124,516 rows across 3,994 identifiable customers. For each customer, three behavioural metrics were computed: **Recency** (days since last purchase), **Frequency** (number of unique orders), and **Monetary value** (total spend in £), collectively known as RFM. These features were standardised using `StandardScaler` before clustering, since K-Means is sensitive to scale differences between variables. The optimal number of clusters (K=4) was selected using the Elbow Method and Silhouette Score (0.587), then **one-way ANOVA** was applied to test whether customer spending differs significantly across countries — producing an important negative result (F=1.66, p=0.094) that revealed revenue differences between markets are driven by customer *volume*, not spend per customer. Finally, **R²** was calculated per RFM dimension and overall to objectively measure how much of the total variation in customer behaviour the segmentation model explains — the four clusters account for **84.9% of overall variance**, confirming the segmentation is statistically robust and not arbitrary.

---

## Key Findings

- **87.9% of revenue comes from the UK alone** — RetailGlobe is heavily exposed to a single market, with France and Germany each contributing under 3%.
- **147 "Champion" customers (just 3.7% of the base) contribute 20.1% of total revenue (£266,908)** — but Loyal Customers (21.9% of the base) contribute the largest share at 45.4% (£602,401), making them the commercial backbone of the business.
- **10.2% of customers (407) placed only one order and never returned** — a meaningful retention gap, smaller than the common retail benchmark of 25–30% but still representing customers acquired but not converted to repeat buyers.
- **October is the single biggest trading month** (£252K), with Q4 accounting for nearly half of annual revenue — the business is highly seasonal and Christmas gift-buying is the core commercial event.
- **Loyal Customers (21.9% of base) are the single largest revenue contributor at 45.4% (£602K)**, followed by At-Risk (32.5%, £430K), Champions (20.1%, £267K), and Dormant (2.0%, £26K) — the segment revenue distribution is more evenly spread than commonly assumed.
- **International customers spend roughly the same per order as UK customers (£250–£340)** — the UK's revenue dominance is entirely explained by volume, not higher individual spend. ANOVA confirmed this difference is not statistically significant (p=0.094). This reframes the international growth opportunity: RetailGlobe does not need to convert international customers differently — it simply needs more of them.
- **The four-cluster segmentation explains 84.9% of total variance in customer behaviour** (R²=0.849), with Frequency best captured (R²=0.889) and Recency most variable within clusters (R²=0.811).

---
## Visualizations
The charts used to visualize some of the findings:
<img width="1235" height="695" alt="page1_overview" src="https://github.com/user-attachments/assets/be4b0a82-3eae-453b-b9fe-db010028e583" />

<img width="1237" height="698" alt="page2_segments" src="https://github.com/user-attachments/assets/66522d32-e77e-45a3-a4d3-630b24a2557d" />

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Python 3.12 | Core analysis language |
| pandas | Data loading, cleaning, and transformation |
| numpy | Numerical operations |
| matplotlib & seaborn | All visualisations |
| scikit-learn | StandardScaler, KMeans, PCA, silhouette_score |
| scipy | One-way ANOVA (f_oneway) |
| Power BI Desktop | Interactive dashboard (see `/dashboard.pbix`) |
| Jupyter Notebook | Reproducible analysis environment |

---

## Limitations of This Analysis

**These are not afterthoughts — they are important caveats that affect how findings should be used.**

### Data Limitations

- **Single market dominance skews everything.** With 88% of transactions from the UK, any "global" pattern we identify is really a UK pattern. The segments we found may not apply to French, German, or Australian customers, who transact far less frequently and may have completely different buying motivations. This is the same structural problem as the "US Bias" in global datasets — majority-country behaviour gets treated as universal truth. Notably, our ANOVA test was statistically underpowered for small-country groups (35 Portuguese customers vs. 3,410 UK customers), which itself is a symptom of this bias.

- **Wholesale and retail customers are mixed together.** RetailGlobe sells both to small businesses (wholesale, large-volume orders) and directly to individual consumers. These are fundamentally different buyer types with different motivations, budgets, and decision cycles. Clustering them together distorts the segments — a wholesale buyer placing one large seasonal order looks "At-Risk" by our model, when they may actually be entirely satisfied.

- **We only observe customers who purchased.** This dataset contains no information about visitors who browsed but didn't buy, customers who churned before this period, or prospects who never converted. Our segments describe existing buyers only — we cannot say anything about why customers chose RetailGlobe in the first place, or why potential customers didn't.

- **13 months is a short window.** RFM values are sensitive to the time period chosen. A customer who bought heavily in 2009 and 2010 but paused in 2011 looks "Dormant" in our model, when they may be a historically loyal customer. Conversely, a new customer who made one large Christmas purchase looks "At-Risk" by recency, but may return the following year. We simply cannot know from this snapshot.

- **No demographic or firmographic data.** We know nothing about customer age, location (beyond country), company size, or whether they are an individual or a business. This limits the actionability of segments — Emma cannot personalise outreach without knowing who these customers actually are.

### Methodological Limitations

- **Segment names are interpretive, not mathematical.** Labels like "Champions" and "Dormant" are assigned by a human after inspecting the cluster statistics — they are not outputs of the algorithm. The K-Means model produces numbered clusters; we chose the names. Another analyst might reasonably name them differently.

- **K-Means assumes spherical clusters.** The algorithm assumes each cluster is roughly circular in feature space, which may not reflect the true structure of customer behaviour. If segments overlap or have irregular shapes, K-Means will force artificial boundaries.

- **RFM ignores what customers buy.** Two customers with identical R, F, and M scores will be placed in the same segment even if one only buys Christmas decorations and the other buys year-round stationery. Product affinity could significantly sharpen the segmentation.

- **R² rises mechanically with K.** Our overall R² of 84.9% is strong, but R² will always increase as we add more clusters. This is why R² is reported alongside — not instead of — the Silhouette Score and business interpretability checks. It is one piece of evidence, not the whole story.

- **ANOVA assumes approximately normal distributions and equal variances.** Customer spend is heavily right-skewed, which technically violates ANOVA's assumptions. The non-significant result (p=0.094) should therefore be treated as directionally informative rather than definitive. A non-parametric Kruskal-Wallis test would be a more rigorous alternative for future work.

---

## What This Analysis Does NOT Tell Us

- **Why customers leave.** We can identify who is at risk, but not whether they left because of price, product quality, a competitor, or simply a life event. Churn reasons require survey data or qualitative research.
- **Whether our segments are stable over time.** These clusters are a snapshot of 2010–2011. Segment membership will shift as customers' behaviour evolves — the model needs to be re-run periodically, not treated as permanent.
- **The causal impact of any marketing action.** If we send Champions a VIP offer and they buy more, we cannot attribute that increase to the offer without a proper control group. Correlation is not causation.
- **Anything about customer satisfaction or NPS.** A high-frequency buyer could be dissatisfied and buying out of inertia or lack of alternatives. Revenue and satisfaction do not always move together.
- **International market dynamics.** The 10 non-UK countries in the dataset each have their own competitive landscape, seasonality, and customer expectations. Treating them as a single "international" bucket in the dashboard masks important differences.
- **Profitability, only revenue.** The Monetary metric is based on gross sales value. We have no data on margins, fulfilment costs, or return rates by customer — a "Champion" who returns 30% of their orders may be far less valuable than their revenue figure suggests.

---

## How to Reproduce This Work

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy jupyter
```

### Steps

1. Clone this repository
2. Place `online_retail.csv` in the root directory (same folder as `notebook.ipynb`)
3. Open and run the notebook:
   ```bash
   jupyter notebook notebook.ipynb
   ```
4. Run all cells in order (Cell → Run All)
5. The notebook will generate `online_retail_segmented.csv` in the same directory
6. Open `dashboard.pbix` in Power BI Desktop and refresh the data source to point to the generated CSV

### Expected outputs
- `online_retail_segmented.csv` — 124,516 rows with Segment and RFM columns appended
- All visualisations rendered inline in the notebook
- Console output documenting row counts at each cleaning step, ANOVA results, and R² scores

### Notes
- Python 3.9+ required
- Random seed is fixed (`random_state=42`) throughout — results are fully reproducible
- Execution time: approximately 60–90 seconds on a standard laptop

---

## Reasoning, Not Just Code

Throughout the notebook, every decision is documented in markdown cells before the code that implements it. The choice of K=4 clusters is justified using both the Elbow curve and Silhouette Score — not just asserted. ANOVA was run not to confirm a hypothesis but to interrogate one — and when it returned a non-significant result, that finding is reported honestly and explained rather than buried. R² is presented alongside its own caveat (that it rises mechanically with K) so the reader understands what it does and does not prove. Segment names are validated against the actual mean R, F, M values of each cluster. Cleaning steps document not just *what* was removed but *why*, and what percentage of data was lost at each step.

The goal is that someone with no data science background — including Emma — could read the markdown narrative alone and understand what was done, why, and what it means for the business.

---

*Capstone Programme · Week 2 · Supervisor: Mohamed Adnan*
