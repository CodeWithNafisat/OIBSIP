# Retail Sales Exploratory Data Analysis

**Transforming raw customer and transaction data into actionable insights on revenue performance, customer behaviour, product trends and regional sales.**

## Project Overview

This project involved an end-to-end exploratory data analysis of retail customer and transaction data obtained from Kaggle. Using Python, I cleaned and integrated two datasets, conducted statistical and exploratory analysis, identified patterns in sales performance and customer behaviour, and developed interactive Power BI dashboards to communicate findings and support business decision-making.

The analysis focused on understanding revenue trends, customer segments, product performance, geographical sales distribution and seasonal demand.

## Business Questions

The analysis sought to answer five key business questions:

1. How does revenue change over time, and are there identifiable seasonal patterns?
2. Which customer demographics and segments contribute the most revenue?
3. Which products and categories generate the highest sales volume and revenue?
4. Which geographical regions perform best and worst?
5. What business strategies can be recommended based on the findings?

## Dataset and Sources

[Retail Customer & Transaction Dataset](https://www.kaggle.com/datasets/raghavendragandhi/retail-customer-and-transaction-dataset)

The original dataset contained four tables. For this analysis, I selected two relevant datasets:

There were two source files. `customers.csv` has 5,000 records and 12 columns covering demographics and location, such as age, gender, city, state and preferred channel. `transactions.csv` has 32,295 records and 10 columns covering transaction date, product name and category, quantity, price, discount, store location and payment method.

The datasets were integrated using `customer_id`.

## Data Cleaning and Preparation

Data preparation was performed using Python and Pandas. The process involved profiling, handling missing values, correcting data types, treating outliers and preparing the datasets for analysis.

### 1. Customer Data Cleaning

- Identified missing values and inconsistencies across customer attributes.
- Removed records with missing customer IDs because they could not reliably be linked to transaction records.
- Converted age and date fields to appropriate data types.
- Standardised categorical text values.
- Removed non-analytical and personal information, including names, email addresses and street addresses.
- Retained six analytical columns: customer ID, age, gender, city, state and preferred shopping channel.

**Result:** I reduced the customer dataset from 5,000 records and 12 columns to 4,699 records and six analytical columns.

### 2. Transaction Data Cleaning

- Profiled missing values and assessed data quality across transaction attributes.
- Handled missing numerical values using median imputation for quantity, price and discount.
- Used product names to recover missing product categories through category mapping.
- Removed records with missing product names and records whose product categories could not be reliably recovered.
- Standardised categorical values and corrected data types.
- Identified extreme values in numerical variables using summary statistics and boxplots.
- Applied winsorization at the 5th and 95th percentiles to quantity, price and age where applicable.

**Result:** I reduced the transaction dataset from 32,295 records and 10 columns to approximately 30,344 records while retaining the analytical fields required for the project.

### 3. Data Integration and Feature Engineering

- Merged the cleaned customer and transaction datasets using `customer_id`.
- Excluded records with missing customer details after the left join.
- Created additional analytical variables, including:
  - `revenue`: Quantity × Price
  - `year`: Extracted from transaction date
  - `month`: Extracted and ordered chronologically
  - `quarter`: Derived from transaction date
  - `age_group`: Customer age segmentation.

**Final dataset:** 27,937 records, approximately 28K integrated transaction records.

The revenue analysis used winsorized quantity and price values. Therefore, the reported revenue figures represent adjusted analytical values rather than unmodified raw sales totals.

## Exploratory Data Analysis

## Analysis

I started with descriptive statistics (mean, median, mode and standard deviation) for the numeric variables. From there I looked at revenue over time by year, month and quarter, then at customers by age, gender and age group. On the product side I looked at the top 10 products by units sold, revenue by category, and quantity against revenue per product. I ran a correlation check of the numeric variables against revenue, and finished with revenue and quantity by state, plus a city-level look at the top state.

## Key Findings and Business Insights

### 1. Revenue Trends and Seasonality

- Revenue increased from 692,785 in 2020 to 7,466,081 in 2024.
- Q4 generated the highest quarterly revenue at 7,454,433, while Q2 recorded the lowest at 3,713,007.
- November and December were the strongest months, generating 2,900,460 and 3,066,801 respectively.
- March recorded the lowest monthly revenue at 1,155,325.
- Revenue dropped to 1,031,550 in 2025. This requires further investigation because the dataset may contain an incomplete year.

**Business implication:** The concentration of revenue towards the end of the year highlights the importance of seasonal inventory planning and targeted promotional campaigns.

<img width="1366" height="768" alt="Screenshot (234)" src="https://github.com/user-attachments/assets/ccc8160d-a0f0-487c-9865-05a04253c96e" />

### 2. Customer Analysis

- Customers aged 30–59 contributed the highest revenue at 15.1M.
- Young adults contributed approximately 6.1M.
- Female customers generated 10.8M compared with 9.7M from male customers.
- Adult women represented the highest-revenue demographic segment, contributing 7.7M.

**Business implication:** Customer segmentation can support more targeted marketing campaigns, customer engagement and promotional strategies.

<img width="1366" height="768" alt="Screenshot (43)" src="https://github.com/user-attachments/assets/db8609f6-92e4-4bed-bf74-31ab9fc2117f" />

### 3. Product and Category Performance

- Furniture generated the highest category revenue at 4.7M.
- Smartphones followed at 4.0M, while laptops generated 3.0M.
- The iPhone 13 recorded the highest sales volume at 1,061 units, generating 847,855 in revenue.
- Bed Frame generated the highest individual product revenue at 1,023,041 from 938 units.

The analysis demonstrated that products with the highest sales volume do not necessarily generate the highest revenue.

Price showed a very strong correlation with revenue. However, this relationship must be interpreted in the context of the revenue calculation, which is derived from quantity multiplied by price.

**Business implication:** Product strategy should consider both sales volume and revenue contribution rather than relying exclusively on units sold.

### 4. Geographical Sales Performance

- California led geographical performance, generating 3.9M in revenue and 6,563 units sold.
- Texas followed with 2.8M in revenue.
- Massachusetts recorded the lowest performance among the compared states, generating 600,608 in revenue and 1,055 units sold.
- Within California, San Diego generated the highest revenue at 855,505, while Los Angeles recorded the highest sales volume at 1,440 units.

**Business implication:** Regional sales strategies should account for differences in revenue generation and sales volume across locations.

<img width="1366" height="768" alt="Screenshot (45)" src="https://github.com/user-attachments/assets/05907991-6979-4140-abbd-e5a67ca0afb0" />

## Power BI Dashboard

To complement the Python analysis, I developed interactive Power BI dashboards to visualise sales performance, customer demographics, product trends and geographical distribution.

The dashboards allow users to explore key business metrics, compare performance across categories and identify patterns using interactive filters.

### Dashboard Preview

<img width="958" height="543" alt="Screenshot (243)" src="https://github.com/user-attachments/assets/f48b989a-e44d-4ad2-b9d8-520365e24da9" />


<img width="951" height="531" alt="Screenshot (244)" src="https://github.com/user-attachments/assets/97be851b-627a-4aaf-acdd-0bc1845ca665" />


## Business Recommendations

Based on the findings, the following actions are recommended:

1. **Strengthen seasonal planning:** Increase inventory readiness and marketing activities ahead of November and December.
2. **Prioritise high-revenue categories:** Focus product strategy on Furniture, Smartphones and Laptops.
3. **Improve customer targeting:** Develop targeted campaigns for customers aged 30–59, particularly high-contributing adult female customers.
4. **Optimise regional strategies:** Maintain focus on strong-performing markets such as California and Texas while investigating underperforming regions.
5. **Differentiate geographical objectives:** Prioritise San Diego for revenue generation and Los Angeles for sales volume.
6. **Investigate the 2025 revenue decline:** Verify data completeness before interpreting the decline or using it for forecasting.


## Tech Stack

Python, pandas, NumPy, Matplotlib, Seaborn, SciPy (winsorization) and Jupyter Notebook. Techniques used: data cleaning, regex, imputation, outlier treatment, feature engineering, time series analysis and customer segmentation.


## How to Run the Project

### 1. Clone the repository

```bash
git clone https://github.com/CodeWithNafisat/OIBSIP.git
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
├── screenshots/
├── Cleaned_retail_data.csv
├── customers.csv
├── Retail_Sales_EDA.ipynb
├── README.md
├── RetailSalesDashboard
└── transactions.csv
```

## Links

- [Notebook](https://github.com/CodeWithNafisat/OIBSIP/blob/main/DataAnalytics-L1-EDARetailSales/EDA_Retail_Sales.ipynb)
- [More projects from this internship (OIBSIP)](https://github.com/CodeWithNafisat/OIBSIP)
- [GitHub profile: CodeWithNafisat](https://github.com/CodeWithNafisat)

---

*This project demonstrates an end-to-end analytical workflow, from raw data preparation and integration to exploratory analysis, business intelligence reporting and data-driven recommendations.*

