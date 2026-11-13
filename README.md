# Second-hand House Price Analysis (Saraburi, Thailand)

A **proof-of-concept** project that predicts the sale price of second-hand houses in Saraburi province, using real data from my family's second-hand house business.

> **Status:** This is a proof of concept built on 37 sold houses from a single business. The project focuses on the analytical approach and the reasoning behind each methodological decision. The resulting numbers should not be used for real-world pricing decisions yet.

## Motivation

From talking with customers, price is the key factor in whether they decide to buy. A house that is priced correctly from the start attracts more interest and tends to sell faster. Some homeowners are unsure how much to ask for, and some set the price too high, which hurts the sale.

This project tries to turn the business owner's experience into a model that gives a reference price, while making the most of the limited data available.

## Key Results

| Item | Result |
|---|---|
| Data | 37 sold second-hand houses (36 used in the model) |
| Model | Linear regression on log(price) |
| Features | Bedrooms, condition score, house type, area |
| Validation | Leave-one-out cross-validation (LOOCV) |
| R-squared (LOOCV) | 0.810 |
| Median error | about 170,000 THB (13.4%) |
| Best baseline (mean price by house type) | about 308,000 THB (21.3%) |
| RMSE vs best baseline | about 47% lower (0.187 vs 0.350, log price units) |
| Houses predicted within 20% error | 26 of 36 (72%) |
| Houses with error above 30% | 4 of 36 (11%) |

The model is not consistently accurate for every house, and 4 houses had errors above 30%.

## Data

- **Source:** The business's Facebook posts advertising houses for sale.
- **Raw data:** 2,432 rows. After removing duplicates, 102 unique houses remain (all statuses: sold, active, and withdrawn). Filtering to sold houses and excluding newly built houses leaves 37 houses, which are the data used in the analysis.
- **Target:** `soldprice`, the final closing price after the whole sale process.
- **Condition score (1 to 5):** Given by the company's assessor during the inspection before a house is accepted for sale, using the company's own criteria (exterior structure, interior condition, overall appearance, and so on). It is therefore known before the sale price exists.

### This notebook is view-only

The data is confidential business data and contains private information, so it is not published in this repository. Readers therefore cannot re-run the notebook, but all code, outputs, and charts are saved in the notebook and can be read in full. Columns containing private information are hidden in the notebook.

## Approach

1. **Data cleaning and feature engineering:** Standardized text categories, converted prices to numbers, translated Thai labels, and grouped sub-districts into 5 areas.
2. **Exploratory data analysis:** Distributions, group comparisons, correlations, and scatter plots by renovation status.
3. **Predictive model:** Linear regression on log(price) with LOOCV. The log transform is used because condition, house type, and location are likely to change price by a percentage rather than a fixed amount.
4. **Evaluation:** Errors converted back to THB and percentages, then compared with two baselines (overall mean, and mean by house type).
5. **Explanatory models (statsmodels OLS):** Three models (A, B, C) to understand which variables relate to price.

## Findings

| Variable | Coefficient (log price) | Approx. effect on price | p-value |
|---|---|---|---|
| Bedrooms (per room) | 0.298 | about +35% | 0.000 |
| Condition score (per point) | 0.150 | about +16% | 0.000 |
| Townhouse (vs single house) | -0.355 | about -30% | 0.000 |

- House condition, number of bedrooms, and house type are **significantly related to price**.
- **Renovation shows no evidence of raising the price once condition is controlled for** (p = 0.636 in modelB). What relates to price is better condition. Renovation and condition score are correlated (r = 0.67), and removing the score makes renovation look significant (modelC, p = 0.001), which shows the two variables explain price in an overlapping way.
- Renovated and non-renovated houses with the same score can be compared at only 2 score levels (3.5 and 5.0), so no firm conclusion can be drawn yet.

## Scope and Limitations

- Only 37 houses from one business, so results are preliminary.
- The model covers **second-hand single houses and townhouses in Saraburi only.** Twin houses will be reconsidered once at least 10 sold second-hand twin houses are available.
- Land area was not used because it is missing for many houses, and house age was not collected.
- The threshold for how far an asking price can differ from the reference price has not been defined, because the sample is too small to set it reliably.

## Intended Use

If developed further with more data, the model would give a reference price from a house's bedrooms, condition score, house type, and area. The business could use it to advise homeowners who have no price in mind, and as a comparison for those who have already set an asking price.

## Next Steps

Collect sold-house data from other second-hand real estate businesses in Saraburi to build a more accurate model.


## Repository Structure

```
├── house_price_analysis.ipynb   # full analysis notebook (view-only)
└── README.md
```
