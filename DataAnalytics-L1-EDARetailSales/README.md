# Retail Sales Exploratory Data Analysis

## Business Problem

As a data analyst supporting a retail business, I wanted to understand **what is driving sales, who the main customers are, which products generate the most revenue, and what trends management should pay attention to**.

The analysis answers questions around sales trends, customer demographics, product performance, and regional performance.

## Analysis Performed

The project includes:

* Descriptive statistics for numerical variables
* Monthly and quarterly sales trend analysis
* Customer age-group and gender analysis
* Top 10 best-selling products
* Revenue by product category
* Correlation analysis using a heatmap
* Additional analysis of regional sales performance
* Business observations and insights after each visualization

## Key Findings

* Sales showed **strong growth from 2020 to 2024**, with **Q4 having the highest sales**.
* **Adults** were the main customer group, with **adult females generating the highest revenue**.
* **Furniture** was the highest-revenue category.
* The product with the highest sales quantity did not necessarily generate the highest revenue.
* **California** was the best-performing state, with **6,563 units and 3,896,917 in revenue**.
* **Massachusetts** had the lowest performance, with **1,055 units and 600,608 in revenue**.
* In California, **San Diego** had the highest revenue (**855,505**), while **Los Angeles** had the highest quantity (**1,440 units**).

## Recommendations

1. **Prepare for Q4:** Increase inventory and marketing before November and December to meet higher demand.

2. **Focus on high-revenue products:** Give more attention to **Furniture** and other products that generate higher revenue.

3. **Target adult customers:** Create targeted promotions for **adult customers**, especially adult females.

4. **Focus on strong markets:** Continue investing in **California and Texas**, while identifying ways to improve weaker markets such as Massachusetts.

5. **Focus on top cities:** In California, consider investing more in **San Diego** for its revenue performance and **Los Angeles** for its sales volume.

## Conclusion

The analysis shows strong sales growth from 2020 to 2024, with Q4 being the strongest sales period. Adults were the main customer group, while adult females contributed the highest revenue. Furniture was the leading revenue-generating category.

California was the strongest-performing state, while Massachusetts recorded the lowest performance. The findings provide useful insights for **inventory planning, marketing, customer targeting, product strategy, and regional investment**.

## Dataset

The dataset was obtained from Kaggle:

[Retail Customer & Transaction Dataset](https://www.kaggle.com/datasets/raghavendragandhi/retail-customer-and-transaction-dataset)

## Tools Used

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* SciPy
* Jupyter Notebook

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
