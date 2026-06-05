# Hawaii Google Maps Reviews

## Introduction

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Firstly, we perform an inner merge on `gmap_id` so only the reviews that are linked to a real business remain. This is necessary because we require both reviews and business metadata so we couldn’t perform a left, right, or outer merge. Then we remove price levels that are not some form of `$` as everything else would be nonsensical as the main form of currency in Hawaii is USD.

### Univariate Analysis

<iframe
src="assets/customer-rating-distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>

This is a bar graph of the categorical variable customer rating. From this, we can see that the distribution of ratings is skewed left with a majority of reviews being within the `5` category which may bias our findings if we only use frequency for our analysis, as the underlying distribution is inherently skewed.

<iframe
src="assets/business-count-by-price-level.html"
width="800"
height="600"
frameborder="0"
></iframe>

This is a bar graph of the categorical variable of price levels (`$`, `$$`, `$$$`, `$$$$`). From this, we can see that most of the counts lie in the first two price levels which results in a right skewed distribution.

### Bivariate Analysis

<iframe
src="assets/ratings-by-price-level.html"
width="800"
height="600"
frameborder="0"
></iframe>

This is a stacked bar chart which describes the distribution of ratings over each price level. Furthermore, it allows us to see that the frequency of ratings with a `5` tends to increase as the price level increases and other ratings tend to decrease in proportion.

### Interesting Aggregates

## Assessment of Missingness

### NMAR Analysis

We think price is NMAR.

### Missingness Dependency

Our first missingness permutation test is:

**Null Hypothesis:** The distribution of `avg_rating` is the same for when `hours_missing` is true and when `hours_missing` is false.

**Alternative Hypothesis:** The distribution of `avg_rating` is different for when `hours_missing` is true and when `hours_missing` is false.

After running a permutation test we got a p-value of `.178` which is larger than our significance level of `.05`, therefore we fail to reject the null hypothesis and conclude that the missingness of hours may be independent of the average rating.

<iframe
src="assets/average-rating-by-hours-missingness.html"
width="800"
height="600"
frameborder="0"
></iframe>

Our second missingness permutation test is:

**Null Hypothesis:** The distribution of `num_of_reviews` is the same for when `hours_missing` is true and when `hours_missing` is false.

**Alternative Hypothesis:** The distribution of `num_of_reviews` is different for when `hours_missing` is true and when `hours_missing` is false.

After running a permutation test we got a p-value of `.001996` which is less than our significance level of `.05`, therefore we reject the null hypothesis and conclude that the missingness of hours depends on the `num_of_reviews`.

## Hypothesis Testing

**Null Hypothesis:** The distribution of customer review ratings is independent of business price level.

**Alternative Hypothesis:** The distribution of customer review ratings differs across business price level.

**Test Statistic:** Chi-square

**Significance Level:** `.05`

**p-value:** `.009`

We conclude that the distribution of customer review ratings differs across business price levels with p-value less than `.05`.

This is effective in answering our main research question as this will show across all reviews that ratings are affected by the price level that the reviewed business is.

<iframe
src="assets/hypothesis-test-distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>

## Framing a Prediction Problem

**Prediction Problem:** We developed a regression model to predict a business's average Google Maps rating (avg_rating) utilizing information about the business, including number of reviews and price level

**Response Variable:** avg_rating, we chose this variable becasue a business's average rating is an important of a customer's satisifaction and overall success.

**Applied Features:**

- `num_of_reviews`: Number of reviews provides info about a business's popularity, possibly associated with average rating
- `price`: Price level could influence influence a customers expectations and affect ratings

**Evaluation Metric:** R², With this being a regression problem, R² allows us to measure how much variance is explained by model.

## Baseline Model

Our baseline model is a **Linear Regression** model that predicts a business's average google Maps rating (`avg_rating`).

**Features:** 
Baseline model wil use two features:

- `price` (ordinal)
- `num_of_reviews` (quantitative)

**Feature Transformations:**

Since `price` is an ordinal categorical variable, it was encoded using `OrdinalEncoder` with the following ordering: `$ < $$ < $$$ < $$$$`

A logarithmic tranformation (`log1p`) was applied to `num_of_reviews` to account for large differences in review counts across a competitive business landscape.

These preprocessing steps and fitting of the model were implmented in a sklearn pipeline.

**Model Performance:**

- R² - 0.048

This baseline model perfomance appears relatively weak, as it currently only explains about 4.8% of variance in business ratings . 

## Final Model

**Additional Features:**

- `zipcode` - captures geographic informatio nabout business location, customer demogrpahic could be different across Hawaii as this could influence business ratings.
- `description_length` - Business's with longer description may provide info to customers about establishment, as it is possibly associatedwith satisfaction of customer.

Final model uses 4 features:
- `price` (ordinal)
- `num_of_reviews` (quantitative)
- `zipcode` (nominal)
- `description_length` (quantitative

**Feature Transformations:**

Since `price` is an ordinal categorical variable, it was encoded using `OrdinalEncoder` with the following ordering: `$ < $$ < $$$ < $$$$`

A logarithmic tranformation (`log1p`) was applied to `num_of_reviews` to account for large differences in review counts across a competitive business landscape.

`zipcode` was encoded using `OneHotEncoder`  because zip codes are nominal categories without orderings.

`description_length` is a numerical feature that counts words in description.

Preprocessing and fitting of model was implemented in sklearn pipeline

## Fairness Analysis

To evaluate model performance across groups, we will compare businesses with relativel low reviews agaisnt business with many reviews.

**Groups:**
- Group X: Low-review businesses (below the median # of reviews)
- *Group Y: High-review businesses (at or above median # of reviews

**Evaulation Metric:** Because our prediction problem is a regression question, we will utilize `R²` as our evaluation metric.

**Hypotheses:** T

**Null -** The model is fair, any difference in `R²` between low/high review businesses is random chance.

**Alternate -** The model is unfair, model achieves higher `R²` for high review business than low review business.

**Test Statistic:** R²(high-review businesses) - R²(low_review businesses)
Large positive values provide evidence for alternate

**Significance Level:** α = 0.05

**Results:**

- Observed Statistic: 0.04718911054922892
- P-value: 0.004995004995004995

**Conclusion:** 0.004995004995004995 < .05, we reject the null. There is evidence that model performs better for high-review businneses.


