# House Price Prediction

I built a regression model that predicts house prices from 18 property, location and neighbourhood features, using a dataset of 50,000 listings. The model reaches an R² of 0.998 on a held-out test set, with an RMSE of 19,941 and an MAE of 15,954. I set the success target before modelling: RMSE had to stay below 20% of the maximum price in the data (399,295). It came in well under that.

## The Problem

Pricing a property by guesswork and manual research leads to two expensive mistakes. If you overprice, the property sits on the market, goes stale, and you end up making steep price cuts later. If you underprice, it sells fast but you leave profit on the table, or make none at all. The aim here was a pricing estimate that is consistent and evidence-based, so it helps sell faster and get more out of each square foot.

I had three kinds of users in mind. Real estate agencies could use it to give clients quick, accurate valuations. Banks could use it to check that a house put up as collateral actually matches the value of the loan. Property developers could use it to estimate their return before buying land or starting a building phase.

Because those users need to trust the number, I wanted a model that is accurate and also easy to explain, so I could show what is driving each valuation.

## The Data

Dataset link : https://www.kaggle.com/datasets/miadul/house-price-prediction-dataset?resource=download

The dataset has 50,000 records and 19 columns, with no missing values and no duplicates. The target is `price`, which ranges from about 91,000 to 1,996,000 with a mean of around 1,030,000.

The 18 features fall into a few groups:

- **Property:** area, bedrooms, bathrooms, floors, age
- **Amenities:** garage, parking, garden, security
- **Nearby facilities:** school, hospital, shopping mall, public transport
- **Location:** distance, and a location tier (low, medium, premium)
- **Neighbourhood:** crime rate, population density, and income level (low, mid, high)

I split the data 80/20 into 40,000 training rows and 10,000 test rows, with a fixed random seed.

## How I Approached It

I started by auditing the data, then moved into EDA, then built a preprocessing pipeline and compared models using cross-validation on the training set only. The test set was kept for the final evaluation.

A few decisions shaped the work:

- **Everything lives inside a scikit-learn pipeline.** Scaling and one-hot encoding are fitted only on the training portion of each cross-validation fold, so nothing leaks in from validation or test data.
- **Numeric features are standardized.** This puts the coefficients on the same scale, which lets me compare how much each feature matters.
- **Categorical features are one-hot encoded with the first level dropped**, to avoid redundant dummy columns in a linear model. The baselines are the low location tier and high income level.
- **Feature selection (RFE) is also inside the pipeline.** That way it is re-run within each fold, and the comparison between "all features" and "top 10" is fair.
- **I defined the error target first.** Judging the model against a threshold tied to the price range seemed more meaningful than just reporting R².

## What the EDA Showed

The data is very regular. Most numeric features are close to uniformly distributed, and the box plots showed no extreme outliers, so I did not apply any outlier treatment.

The clearest signal is area. Price rises almost linearly with it, from roughly 100,000 for the smallest homes (about 500 sq ft) up to around 2,000,000 for the largest (about 5,000 sq ft). Plotting price against bedrooms, bathrooms, floors, age, distance, crime rate and population density did not show anything comparably obvious.

One thing worth pointing out: the violin plots made location and income level look irrelevant, because the price spread from area drowns everything else out. The model told a different story once area was controlled for (more on that below), so I would not trust the plots alone on those two.

## Modelling

Since the area-to-price relationship looked linear, I used linear regression as the main model and compared it against a few alternatives.

**Feature selection.** I compared linear regression on all 20 encoded features against a version that used RFE to keep only the best 10, using 5-fold cross-validation. The full feature set won, with a CV RMSE of about 20,014 against 21,767 for the top-10 version, and a CV MAE of about 15,982 against 17,384. RFE kept area, bedrooms, bathrooms, age, distance, crime rate, the medium and premium location levels, and the low and mid income levels. Dropping the rest (amenities, nearby facilities, floors, population density) made the model about 9% worse on RMSE, so I kept everything.

<img width="1341" height="138" alt="Screenshot (40)" src="https://github.com/user-attachments/assets/293ad758-588e-499e-9bc9-64e6fd349d9c" />

**Regularization.** I also wanted to know whether Ridge regression would help. I tried it with a default alpha, then ran `RidgeCV` over alphas from 0.001 to 1000 with 5-fold CV (best alpha was 0.1). None of it moved the needle, which I cover below.

## Evaluation

On the 10,000 held-out test records, plain linear regression on all features gave an RMSE of 19,940.85, an MAE of 15,954.17 and an R² of 0.998. Ridge and RidgeCV landed within a fraction of a unit of that on every metric, so regularization made no real difference here. 

<img width="1349" height="308" alt="Screenshot (41)" src="https://github.com/user-attachments/assets/c385e10c-d9a0-4a6e-bbe7-8f814e8b3e87" />


Against the target I set at the start, an RMSE of 19,941 is far below the 399,295 ceiling. Compared with the average price, that works out to roughly 1.9% for RMSE and 1.5% for MAE (my own calculation from the dataset mean).

I also checked the usual regression diagnostics. Actual versus predicted values follow the diagonal, the residuals show no visible pattern and a fairly constant spread, and the residual distribution is roughly bell-shaped and centred on zero.

<img width="1366" height="768" alt="Screenshot (42)" src="https://github.com/user-attachments/assets/9216deda-e68a-4e7e-bece-9969474b09ec" />


## What Drives the Price

Area is by far the biggest factor. Its standardized coefficient is about +454,000, which works out to roughly 350 price units per extra square foot (I got that by dividing the coefficient by the standard deviation of area, so treat it as an estimate).

After area, the next largest effects are location tier and income level. A premium location adds about 80,000 over the low-location baseline, and a medium one adds about 40,000. Low-income areas come in about 50,000 below the high-income baseline, and mid-income areas about 30,000 below. Beyond that, age pulls the price down (about −29,000 per standard deviation), bedrooms push it up (about +20,500), and greater distance (about −15,000) and higher crime rate (about −8,800) both lower it.

The amenity flags barely matter, at roughly 2,400 each for garage, parking, garden and security. The nearby-facility flags sit within about 230 of zero.


## Takeaways

- Size explains most of the price. Location tier and neighbourhood income come next, followed by age, distance and crime rate.
- Cutting down to fewer features hurt the model in cross-validation, so keeping the small-signal features was the right call.
- A plain linear model was enough. Regularization added nothing, which backs the choice of something simple and explainable.
- EDA plots alone understated the categorical features. The coefficients were the better guide.

One caveat for accuracy: the data is very clean and regular, with near-uniform features and an almost linear area-to-price relationship. That is why the fit is so tight. It shows the pipeline works well on this data, but I would not expect the same accuracy on messy real-world listings.

## Practical Use

For agencies, this could be a quick, explainable first-pass valuation in place of manual comparable research. For lenders, it gives an independent estimate to sanity-check collateral against a loan. For developers, it shows how much size, location and neighbourhood move value before money is committed.

To be clear about scope, the notebook measures prediction accuracy only. It does not measure time to sell, revenue, or the cost of pricing errors, and the model has not been deployed.

## Tech Stack

Python, pandas, NumPy, scikit-learn (Pipeline, ColumnTransformer, StandardScaler, OneHotEncoder, KFold, cross_validate, RFE, LinearRegression, Ridge, RidgeCV), seaborn, Matplotlib and Jupyter.

## Project Structure

```
├── Screenshots/
├── HousePricePrediction.ipynb   # Audit, EDA, modelling and evaluation
├── house_price_50k.csv                                
└── README.md
```

## How to Run

```bash
git clone https://github.com/CodeWithNafisat/OIBSIP.git
cd OIBSIP/DataAnalytics-L2-HousePricePrediction

pip install pandas numpy scipy seaborn matplotlib scikit-learn jupyter

jupyter notebook HousePricePrediction.ipynb
```

Keep `house_price_50k.csv` in the same folder as the notebook, since the path in the code is relative, then run all the cells. The random seed is fixed at 42, so the splits and cross-validation results are reproducible.
