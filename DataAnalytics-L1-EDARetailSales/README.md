# Retail Sales Exploratory Data Analysis

**Turning raw customer and transaction data into revenue, product, customer and regional insights, and clear next steps for the business.**

---

## 📌 Project Overview

A retail business needs to know **when it sells, what it sells, who buys, and where revenue comes from**. This project takes two messy raw datasets (customers and transactions), cleans and merges them, and runs an end-to-end exploratory analysis. The result is a set of data-backed findings and recommendations on seasonal planning, product focus, customer targeting and regional strategy.

---

## 🎯 Business Questions

1. How has revenue changed over time, and is there a seasonal pattern?
2. Who are the customers (age, gender), and which segments drive the most revenue?
3. Which products and categories perform best, and does selling the most units mean earning the most revenue?
4. Which states and cities perform best and worst?
5. What should the business do next based on these findings?

---

## 🗂️ Dataset
The dataset was obtained from Kaggle:

[Retail Customer & Transaction Dataset](https://www.kaggle.com/datasets/raghavendragandhi/retail-customer-and-transaction-dataset)

Two datasets were used:

customers.csv: 5,000 records, 12 columns, Customer demographics and location (age, gender, city, state, preferred channel, etc.) 
transactions.csv: 32,295 records, 10 Transaction date, product name and category, quantity, price, discount, store location, payment method.

The two tables were joined on `customer_id`. The final cleaned dataset is about **28.6K transactions**, exported as Cleaned_retail_data.csv.

---

## 🧹 Data Cleaning & Preparation

Data quality issues were the biggest hurdle in this project. Each step below was a deliberate decision:

**Profiling.** Found missing values in most customer columns and mismatched data types (`age` stored as text, `registration_date` not a datetime). Transaction data had missing values in every field except ID, customer and date, each under 3%.

**Missing value strategy (chosen by variable type and risk of bias):**

| Field | Treatment | Rationale |
|---|---|---|
| Age, Quantity, Price, Discount | Filled with **median** | Skewed numeric fields with extreme values; median is robust to them |
| Gender | Filled with **mode** | Categorical variable |
| City, State, Preferred Channel, Store Location, Payment Method, Product Name | **Dropped** rows | Too few to matter, and no reliable way to infer the true value without introducing bias |
| Product Category | **Recovered via product-name mapping**; 19 unmatched rows dropped | Used existing data to restore values instead of discarding them |

**Other preparation steps:**
- Converted data types (age to integer, dates to datetime, categorical text standardised with proper casing).
- Removed PII and non-analytical columns (name, email, street address, zip code, registration date).
- **Cleaned and validated phone numbers with regex** (extension extraction, format standardisation, 10-digit validation) as a skill-building exercise, then dropped the field since it had no analytical value.
- **Merged** customers and transactions (left join), then removed rows with missing customer details after the merge (5.5% of the data). I chose exclusion over bulk imputation to avoid biasing results with repeated assumed values.
- **Outlier handling:** boxplots and summary statistics revealed extreme values in quantity, price and age. **Winsorization (5% at each tail)** was applied to numeric fields before revenue was calculated, limiting the influence of extremes.
- **Feature engineering:** `revenue` (quantity × price), `year`, `month` (ordered), `quarter` and `age_group`.

---

## 🔍 Exploratory Analysis

- **Descriptive statistics:** mean, median, mode and standard deviation for the numeric variables.
- **Time series:** yearly, monthly and quarterly revenue trends.
- **Customer demographics:** age distribution, gender distribution, revenue by gender, by age group, and by age group × gender.
- **Product analysis:** top 10 products by units sold, revenue by category, and quantity versus revenue per product.
- **Correlation analysis:** heatmap of numeric variables against revenue.
- **Regional analysis:** revenue and quantity by state, plus a city-level drill-down for the top state.

---

## 💡 Key Insights

### 📈 Revenue Trends
- Revenue grew steadily from **692,785 (2020)** to **7,466,081 (2024)**.
- 2025 shows a sharp drop to **1,031,550**. The analysis flags this for investigation, because it could reflect a genuine decline or an incomplete year of data.
- **Q4 is the strongest quarter** (7,454,433) and **Q2 the weakest** (3,713,007). **December (3,066,801)** and **November (2,900,460)** are the peak months, and **March** is the lowest (1,155,325). This suggests a seasonal end-of-year pattern.

### 👥 Customers
- Adults (30–59) are the core segment, generating **15.1M** in revenue against **6.1M** from young adults.
- **Female customers generate the most revenue** (10.8M vs 9.7M for males). **Adult females are the top segment** at 7.7M.
- The age groups were defined as under 30, 30–59 and 60+. The 60+ group had no records, so the customer base is concentrated in younger and middle-aged buyers.

### 🛍️ Products
- **Furniture is the top revenue category** (4.7M), followed by **Smartphones** (4.0M) and **Laptops** (3.0M).
- **Best-selling ≠ highest-earning:** the iPhone 13 leads on units sold (1,061) with 847,855 in revenue, but the **Bed Frame** earns the most revenue (1,023,041) from only 938 units.
- **Price is the dominant revenue driver.** It has a near-perfect correlation with revenue, while quantity, discount and age show little to none.

### 🗺️ Regions
- **California is the top state** on both revenue (3.9M) and quantity (6,563), followed by **Texas** (2.8M revenue).
- **Massachusetts is the weakest state** (600,608 revenue, 1,055 units).
- Within California, **San Diego leads on revenue** (855,505) while **Los Angeles leads on volume** (1,440 units).

---

## ✅ Business Recommendations

1. **Plan for Q4.** Increase inventory and marketing ahead of November and December, the highest-revenue months.
2. **Prioritise high-value categories.** Give more attention to Furniture, Smartphones and Laptops, which bring in the most revenue.
3. **Target adult customers,** especially adult women, with tailored promotions, since they are the top revenue segment.
4. **Double down on strong markets** such as California and Texas, and investigate what is holding back weaker states like Massachusetts.
5. **Tailor city strategy in California.** Invest in San Diego for revenue and Los Angeles for volume.
6. **Investigate the 2025 revenue drop** before making forecasts.

---

## 📊 Visualizations

Charts produced in the notebook include:

- Yearly, monthly and quarterly sales trends
- Customer age distribution and age-group counts
- Revenue by gender, by age group, and by age group × gender
- Top 10 products by quantity and revenue by product category
- Correlation heatmap
- Revenue and quantity by state


---

## 🛠️ Tools & Tech Stack

| Category | Tools |
|---|---|
| Language | Python |
| Data manipulation | Pandas, NumPy |
| Visualization | Matplotlib, Seaborn |
| Statistics | SciPy (winsorization), correlation analysis |
| Techniques | Data cleaning, regex, imputation, outlier treatment, feature engineering, time series analysis, segmentation |
| Environment | Jupyter Notebook |

---

## How to Run the Project

### 1. Clone the repository

```bash
git clone <repository_url>
cd OIBSIP/DataAnalytics-L1-EDARetailSale
```

### 2. Install the required libraries

```bash
pip install pandas numpy matplotlib seaborn scipy jupyter
```

### 3. Run the notebook

Start Jupyter Notebook:

```bash
jupyter notebook
```

Open `Retail_Sales_EDA.ipynb` and run the cells sequentially.

The cleaned dataset generated during the analysis is saved as:

```text
Cleaned_retail_data.csv
```

## Project Structure

```text
DataAnalytics-L1-EDARetailSale/
│
├── README.md
├── Retail_Sales_EDA.ipynb
├── customers.csv
├── transactions.csv
├── Cleaned_retail_data.csv
└── screenshots/
```

## Author

**Nafisat**
Data Analytics | OIBSIP
