# Retail Sales Exploratory Data Analysis

**Turning raw customer and transaction data into revenue, product, customer and regional insights, and clear next steps for the business.**

I took two messy raw datasets, one of customers and one of transactions, cleaned and merged them, and ran an end-to-end analysis to find out when a retail business earns its revenue, what it sells, who buys, and where. The work covers about 28.6K cleaned transactions and ends with concrete recommendations on seasonal planning, product focus, customer targeting and regional strategy.

## The Problem

A retail business needs to know when it sells, what it sells, who is buying, and where the revenue comes from. Raw exports rarely answer those questions directly, so I set out to answer five:

1. How has revenue changed over time, and is there a seasonal pattern?
2. Who are the customers, and which segments bring in the most revenue?
3. Which products and categories perform best, and does selling the most units also mean earning the most revenue?
4. Which states and cities perform best and worst?
5. What should the business do next based on these findings?

## The Data

The dataset was obtained from Kaggle:

[Retail Customer & Transaction Dataset](https://www.kaggle.com/datasets/raghavendragandhi/retail-customer-and-transaction-dataset)

There were two source files. `customers.csv` has 5,000 records and 12 columns covering demographics and location, such as age, gender, city, state and preferred channel. `transactions.csv` has 32,295 records and 10 columns covering transaction date, product name and category, quantity, price, discount, store location and payment method.

I joined them on `customer_id`. After cleaning, the final dataset has about 28.6K transactions and is saved as `Cleaned_retail_data.csv`.

## Cleaning and Preparation

Data quality was the hardest part of this project, so most of my decisions were about cleaning without distorting the results.

**Profiling.** Most customer columns had missing values, and some types were wrong: `age` was stored as text and `registration_date` was not a datetime. In the transaction data, every field except ID, customer and date had missing values, each under 3%.

**Missing values.** I picked the treatment based on the type of variable and the risk of bias:

- **Age, quantity, price and discount** were filled with the median, since these fields are skewed with extreme values and the median is not pulled around by them.
- **Gender** was filled with the mode, as it is categorical.
- **City, state, preferred channel, store location, payment method and product name** were dropped where missing. There were few of them, and I had no reliable way to infer the true value without introducing bias.
- **Product category** was recovered by mapping from product name, which let me keep rows I would otherwise have lost. Only 19 rows could not be matched, and I dropped those.

**Other preparation.**

- I converted data types (age to integer, dates to datetime) and standardized the casing of categorical text.
- I removed personal and non-analytical columns: name, email, street address, zip code and registration date.
- I cleaned and validated phone numbers with regex (extension extraction, format standardization, a 10-digit check), then dropped the column because it added nothing analytically.
- After the left join, I excluded rows with missing customer details, which was 5.5% of the data. I chose exclusion over bulk imputation so that repeated assumed values would not bias the results.
- Boxplots and summary statistics showed extreme values in quantity, price and age, so I winsorized these at 5% on each tail before calculating revenue.
- I engineered `revenue` (quantity × price), `year`, `month` (ordered), `quarter` and `age_group`.

Revenue is calculated from the winsorized values, so the figures below are adjusted numbers, not raw sales totals.

## Analysis

I started with descriptive statistics (mean, median, mode and standard deviation) for the numeric variables. From there I looked at revenue over time by year, month and quarter, then at customers by age, gender and age group. On the product side I looked at the top 10 products by units sold, revenue by category, and quantity against revenue per product. I ran a correlation check of the numeric variables against revenue, and finished with revenue and quantity by state, plus a city-level look at the top state.

## What I Found

### Revenue over time

Revenue grew steadily from 692,785 in 2020 to 7,466,081 in 2024. Then it drops sharply to 1,031,550 in 2025. I did not read that as a real decline, because it could simply be an incomplete year of data, so I would confirm that before forecasting anything.

There is a clear seasonal pattern. Q4 is the strongest quarter at 7,454,433 and Q2 is the weakest at 3,713,007. December (3,066,801) and November (2,900,460) are the peak months, and March is the lowest at 1,155,325. That points to an end-of-year selling season.

<img width="1366" height="768" alt="Screenshot (234)" src="https://github.com/user-attachments/assets/ccc8160d-a0f0-487c-9865-05a04253c96e" />

### Customers

Adults aged 30 to 59 are the core segment, bringing in 15.1M in revenue compared with 6.1M from young adults. I grouped ages as under 30, 30 to 59 and 60+, and the 60+ group had no records, so the customer base sits in the younger and middle-aged bands.

Female customers generate more revenue than male customers (10.8M against 9.7M), and adult women are the top segment at 7.7M.


### Products

Furniture is the top revenue category at 4.7M, followed by smartphones at 4.0M and laptops at 3.0M.

Selling the most units does not mean earning the most. The iPhone 13 leads on units sold (1,061) with 847,855 in revenue, but the Bed Frame earns the most revenue (1,023,041) from only 938 units.

Price is the main driver of revenue. It correlates almost perfectly with revenue, while quantity, discount and age show little to no relationship. Since revenue is defined as quantity × price, part of that result comes from how revenue is built, but it still shows price varies far more than the other inputs.

<img width="1366" height="768" alt="Screenshot (43)" src="https://github.com/user-attachments/assets/db8609f6-92e4-4bed-bf74-31ab9fc2117f" />


### Regions

California is the top state on both revenue (3.9M) and quantity (6,563), followed by Texas at 2.8M in revenue. Massachusetts is the weakest, with 600,608 in revenue and 1,055 units.

Inside California, the two leading cities lead on different measures: San Diego has the highest revenue (855,505) while Los Angeles has the highest volume (1,440 units).

<img width="1366" height="768" alt="Screenshot (45)" src="https://github.com/user-attachments/assets/05907991-6979-4140-abbd-e5a67ca0afb0" />


## Recommendations

1. **Plan for Q4.** Build up inventory and marketing ahead of November and December, the highest-revenue months.
2. **Prioritize high-value categories.** Furniture, smartphones and laptops bring in the most revenue and deserve the most attention.
3. **Target adult customers,** especially adult women, with tailored promotions, since they are the top revenue segment.
4. **Build on strong markets.** Keep investing in California and Texas, and look into what is holding back weaker states like Massachusetts.
5. **Tailor the California strategy by city.** San Diego is the place to push for revenue, and Los Angeles for volume.
6. **Investigate the 2025 drop** before using these numbers for forecasts.

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
├── README.md
├── Retail_Sales_EDA.ipynb
├── customers.csv
├── transactions.csv
├── Cleaned_retail_data.csv
└── screenshots/
```

## Links

- [Notebook](https://github.com/CodeWithNafisat/OIBSIP/blob/main/DataAnalytics-L1-EDARetailSales/EDA_Retail_Sales.ipynb)
- [More projects from this internship (OIBSIP)](https://github.com/CodeWithNafisat/OIBSIP)
- [GitHub profile: CodeWithNafisat](https://github.com/CodeWithNafisat)

