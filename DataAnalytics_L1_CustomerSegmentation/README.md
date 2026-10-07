# Customer Segmentation Using RFM Analysis and K-Means Clustering

Grouping online retail customers by purchasing behaviour so the business can run targeted retention and win-back campaigns instead of one-size-fits-all marketing.

---

## Business Problem

A retailer with thousands of customers cannot treat them all the same. Some buy often and spend heavily, while others bought once and never returned. Without segmentation, marketing budget is spread evenly across very different customers.

**Objective:** Identify distinct customer groups from transaction history so each group can receive a marketing strategy that fits its behaviour.

---

## Dataset

- **Source:** [Online Retail dataset, UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail), included in this repo as `OnlineRetail.xlsx`
- **Size:** 541,909 rows and 8 columns
- **Period covered:** 1 December 2010 to 9 December 2011

**Key features:** `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, `Country`

### Data quality issues found

The audit surfaced problems that would have distorted the segmentation if left alone:

- 135,080 rows with no `CustomerID` (about a quarter of the data)
- 5,268 exact duplicate rows
- 1,454 rows with a missing product description
- 10,624 negative-quantity rows, many linked to cancelled invoices (prefix `C`)
- 2,515 rows with a zero unit price

---

## Approach

1. **Data cleaning and validation**
   - Removed rows with missing customer IDs or descriptions, and dropped duplicates
   - Excluded cancellations, adjustments, and rows with zero or negative quantity or price, so the analysis reflects completed purchases only
   - Result: **392,692 transactions, 4,338 customers, 18,532 invoices, 3,665 products**

2. **Exploratory analysis**
   - Built customer-level metrics and a monthly view of revenue, invoices, and active customers

3. **RFM feature engineering** (one row per customer)
   - **Recency:** days since last purchase, measured from one day after the last transaction in the data, so the result does not depend on today's date
   - **Frequency:** number of unique invoices
   - **Monetary:** total revenue (quantity × unit price)

4. **Preprocessing for clustering**
   - RFM distributions were strongly right-skewed, so a `log1p` transform was applied
   - Features were then standardised so each contributes equally to K-Means distances

5. **Choosing the number of clusters**
   - Tested K = 2 to 10 using both the elbow method (inertia) and silhouette score
   - K = 2 had the highest silhouette score (0.43), well ahead of every other K tested (0.28 to 0.34)

6. **Cluster profiling**
   - Compared clusters on raw means and medians, medians relative to the overall median, and standardised cluster means (heatmap)
   - Visualised results with bar charts, 2D scatter plots, and a 3D RFM plot

---

## Key Findings

**Two distinct customer segments emerged:**

- **Cluster 1: Active, high-value customers** (1,666 customers, 38.4%)
  - Median recency: **16 days**
  - Median frequency: **6 orders**
  - Median spend: **2,061**
  - Roughly 3× the overall median on both frequency and spend

- **Cluster 0: Low-engagement, lower-value customers** (2,672 customers, 61.6%)
  - Median recency: **96 days**
  - Median frequency: **1 order**
  - Median spend: **363**
  - About half the overall median on frequency and spend, and noticeably less recent

**Other observations:**

- Monthly revenue rose sharply from September to November 2011, with November the strongest month. The apparent drop in December is a data artefact: the dataset ends on 9 December, so that month is only partial.
- Frequency and spend are highly skewed, meaning a small number of customers account for far more orders and revenue than a typical customer. This is why the log transform was necessary.

---

## Business Recommendations

- **Cluster 1 (active, high-value): retention and loyalty.** Protect and grow the value of customers who already buy often and recently.
- **Cluster 0 (low-engagement): win-back and reactivation.** Focus re-engagement effort on customers who buy rarely, spend less, and have not purchased recently.

Splitting the customer base this way lets the business focus reactivation budget on lapsed customers while keeping the most valuable customers engaged.

---

## Limitations

- A silhouette score of 0.43 indicates **moderate** cluster separation, not sharp boundaries.
- K = 2 gives a clear high-value versus low-engagement split, but it is a broad segmentation. Finer segments (for example, separating new customers from lapsed ones) were not explored here.
- Cancelled and returned orders were excluded from the segmentation, so return behaviour is not reflected in the customer profiles.

---

## Tools and Technologies

- **Language:** Python
- **Data handling:** pandas, NumPy
- **Machine learning:** scikit-learn (`KMeans`, `StandardScaler`, `silhouette_score`)
- **Visualisation:** Matplotlib, Seaborn, Plotly
- **Environment:** Jupyter Notebook

---

## How to Run

1. **Clone the repository**
   ```bash
   git clone https://github.com/CodeWithNafisat/OIBSIP.git
   cd OIBSIP/DataAnalytics_L1_CustomerSegmentation
   ```

2. **Install dependencies**
   ```bash
   pip install pandas numpy matplotlib seaborn plotly scikit-learn openpyxl jupyter
   ```

3. **Dataset**
   `OnlineRetail.xlsx` is already included in the project folder, and the notebook reads it by that filename. No download is needed.

4. **Run the notebook**
   ```bash
   jupyter notebook CustomerSegmentation.ipynb
   ```
   Run all cells from top to bottom.

---

## Project Structure

```
DataAnalytics_L1_CustomerSegmentation/
├── CustomerSegmentation.ipynb   # Full analysis: cleaning, RFM, clustering, insights
├── OnlineRetail.xlsx            
└── README.md
```

---

## Links

- [Dataset: Online Retail (UCI Machine Learning Repository)](https://archive.ics.uci.edu/dataset/352/online+retail)
- [Notebook](https://github.com/CodeWithNafisat/OIBSIP/blob/main/DataAnalytics_L1_CustomerSegmentation/CustomerSegmentation.ipynb)
- [More projects from this internship (OIBSIP)](https://github.com/CodeWithNafisat/OIBSIP)
- [GitHub profile: CodeWithNafisat](https://github.com/CodeWithNafisat)
