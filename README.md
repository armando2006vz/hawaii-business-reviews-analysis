# Introduction

## Project Overview

Firstly, we chose the Hawaii Google Map Reviews dataset because we are interested in learning about the business side of tourism in locations like Hawaii through feedback from customers. Reviews will provide insight into customer satisfaction, service quality, and business metrics which help us better understand what makes a company “good” through the perspective of a consumer.

By analyzing this data an understanding of what creates negative or positive experiences will be created, potentially helping businesses improve service quality in Hawaii’s tourism market.

## Research Question

Question: Does business price level influence the distribution of customer review ratings?

## Hypotheses

Null Hypothesis: The distribution of customer review ratings is independent of business price level.

Alternate Hypothesis: The distribution of customer review ratings differs across business price levels.

## Purpose

This will provide insight into how businesses might want to value their products as statistically significant differences may point to an association between price level and customer reviews. However, this does not imply a causal relationship.

## The Dataset

The dataset consists of two different files. The first one is called "meta-Hawaii.json.gz" which we turned into a DataFrame called `meta` and contains information about the business such as location, name, and hours. The second one is called "review-Hawaii_10.json.gz" which we turned into a DataFrame called `reviews`, which contains user review information for the businesses in the `meta` dataset. Before cleaning, the `meta` dataset contains `21,507` rows and `reviews` contains `150,4347` rows. 

The columns that are most relevant to our question are `gmap_id`, `price`, `rating`, `avg_rating`, and `num_of_reviews`.

**`gmap_id`:** This column appears in both the `reviews` dataset and the `meta` dataset. It is a unique id for each business and it was primarily used to merge the `reviews` dataset with the `meta` dataset so that each review could be connected to a real business.

**`price`:** This column comes from the `meta` dataset. It represents the business price level, such as `$`, `$$`, `$$$`, and `$$$$`. This is one of the main columns for our question because we are trying to understand whether business price level influences the distribution of customer review ratings.

**`rating`:** This column comes from the `reviews` dataset. It represents what review rating a business received from an individual customer. This is column we are trying to predict because our question is focused on whether the distribution of customer review ratings differs across business price levels.

**`avg_rating`:** This column comes from the `meta` dataset. It represents the average rating of a business across many individuals. We use this column to understand overall customer satisfaction for each business, and it will also play a role in our missingness analysis.

**`num_of_reviews`:** This column comes from the `meta` dataset. It represents the total number of reviews a business has. This gives more context about how much review information is available for each business, and we also use it when analyzing whether missingness depends on other columns.

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning

Firstly, we subset `reviews` to only include `user_id`, `rating`, and `gmap_id` because to answer our question we primarily need the rating and price columns and the rest is for identification and merging. Similarily, we subset `meta` to only include `name`, `gmap_id`, and `price`. Then, we perform an inner merge on `gmap_id` so only the reviews that are linked to a real business remain. This is necessary because we require both reviews and business metadata so we couldn’t perform a left, right, or outer merge. Then we remove price levels that are not some form of `$` as everything else would be nonsensical as the main form of currency in Hawaii is USD. This results in a DataFrame that looks like this:

|     user_id |   rating | gmap_id                              | name       | price   |
|------------:|---------:|:-------------------------------------|:-----------|:--------|
| 1.08979e+20 |        5 | 0x7c0014dcca83e7b1:0x1414a4475d8c17d | Buona Sera | $$      |
| 1.08979e+20 |        5 | 0x7c0014dcca83e7b1:0x1414a4475d8c17d | Buona Sera | $$      |
| 1.12409e+20 |        4 | 0x7c0014dcca83e7b1:0x1414a4475d8c17d | Buona Sera | $$      |
| 1.12409e+20 |        4 | 0x7c0014dcca83e7b1:0x1414a4475d8c17d | Buona Sera | $$      |
| 1.15716e+20 |        5 | 0x7c0014dcca83e7b1:0x1414a4475d8c17d | Buona Sera | $$      |

## Univariate Analysis

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

## Bivariate Analysis

<iframe
src="assets/ratings-by-price-level.html"
width="800"
height="600"
frameborder="0"
></iframe>

This is a stacked bar chart which describes the distribution of ratings over each price level. Furthermore, it allows us to see that the frequency of ratings with a `5` tends to increase as the price level increases and other ratings tend to decrease in proportion.

## Interesting Aggregates

| price   |   avg_rating |   num_of_reviews |
|:--------|-------------:|-----------------:|
| $       |         4.15 |              249 |
| $$      |         4.22 |              371 |
| $$$     |         4.29 |              323 |
| $$$$    |         4.42 |              330 |
| Missing |         4.38 |              103 |

We grouped the `meta` DataFrame by `price` and calculated aggregate statistics for each price level. More specifically, we calculated the mean `avg_rating` and mean `num_of_reviews` to see whether any trends appeared across price levels. From the table, we can roughly see that as price level increases, the mean average rating also increases. Additionally, the average number of reviews is highest for businesses with a `$$` price level, suggesting that `$$` priced businesses may receive more review frequency than the other price groups.

# Assessment of Missingness

## NMAR Analysis

We think price may be NMAR because we hypothesize that whether a business has a listed price could depend on the price level itself. For example, if a business is very expensive, it may be less likely to have `$$$$` displayed because that could deter potential customers. Therefore, the missingness of price may depend on the unobserved value of price itself, rather than being fully explained by other observed columns in the dataset.

## Missingness Dependency

### Missingness of Price Does Not Depend on Another Column

Our first missingness permutation test is:

**Null Hypothesis:** The distribution of `avg_rating` is the same for when `hours_missing` is true and when `hours_missing` is false.

**Alternative Hypothesis:** The distribution of `avg_rating` is different for when `hours_missing` is true and when `hours_missing` is false.

After running a permutation test we got a p-value of `.178` which is larger than our significance level of `.05`, therefore we fail to reject the null hypothesis and conclude that the missingness of hours may be independent of the average rating.


### Missingness of Price Depends on Another Column

Our second missingness permutation test is:

**Null Hypothesis:** The distribution of `num_of_reviews` is the same for when `hours_missing` is true and when `hours_missing` is false.

**Alternative Hypothesis:** The distribution of `num_of_reviews` is different for when `hours_missing` is true and when `hours_missing` is false.

After running a permutation test we got a p-value of `.001996` which is less than our significance level of `.05`, therefore we reject the null hypothesis and conclude that the missingness of hours depends on the `num_of_reviews`.

<iframe
src="assets/hours_reviews_fig.html"
width="800"
height="600"
frameborder="0"
></iframe>

The figure above compares the distribution of review counts for businesses with hours information missing and businesses that present hours. Businesses with missing hours tend to create a review counts distribution that is different, showing that the missingness of `hours` column is related to reviews a business recieves. This is supported by our permutation (p = .001996), providing evidence that the missingness of `hours` isn't independent of `num_of_reviews`.

## Hypothesis Testing

**Null Hypothesis:** The distribution of customer review ratings is independent of business price level.

**Alternative Hypothesis:** The distribution of customer review ratings differs across business price level.

**Test Statistic:** Chi-square

**Significance Level:** `.05`

**p-value:** `.009`

Since the p-value is less than the significance level of 0.05, we reject the null hypothesis. This most likely suggests that the distribution of customer review ratings differs across business price levels.

Furthermore, this helps answer our main research question because it provides evidence that review ratings are not distributed the same way across each price level. However, this does not mean that price level causes higher or lower ratings, since a hypothesis test cannot prove a causal relationship. Instead, the result suggests that there is an association between business price level and customer review ratings.

<iframe
src="assets/hypothesis-test-distribution.html"
width="800"
height="600"
frameborder="0"
></iframe>

## Framing a Prediction Problem

**Prediction Problem:** Can we predict a business’s average Google Maps rating using business metadata such as price level and number of reviews? To do this we will develop a regression model to predict a business's average Google Maps rating (`avg_rating`) utilizing information about the business, including number of reviews (`num_of_reviews`) and price level (`price`).

**Response Variable:** `avg_rating`, we chose this variable becasue a business's average rating is an important metric of a customer's satisifaction and overall success and therefore, is an ideal variable to predict. Furthermore, it is a quantative variable which is needed since we are using a regression and not a classification model. 

**Applied Features and Justification:**

- `num_of_reviews`: Number of reviews provides info about a business's popularity, with more reviews possibly indicating a higher popularity and therefore a positive association with average rating. This is also available during the time of analysis as we are using prior information about the number of reviews. 
- `price`: Price level could influence influence a customers expectations and affect ratings. For example, more expensive products are usually associated with a higher level of satisfaction and possibly a positive associtation with average rating. Furthermore, a price level is already set so it is available at the time of analysis. 

**Evaluation Metric:** We used R² as our evaluation metric because this is a regression problem where the goal is to understand how well business metadata explains variation in average Google Maps ratings. Since `avg_rating` is a continuous quantitative variable, R² is useful because it measures the proportion of variance in ratings that is explained by the model. This makes it easier to compare the two final models in terms of how much additional explanatory power the added features provide. Furthermore, other metrics such as root mean square error (RMSE) only focus on the average size of prediction errors, while R² focuses on how well the model explains differences across businesses which is more useful for our question about how well features are at predicting average rating.

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

- R²: 0.048

This baseline model perfomance appears relatively weak, as it currently only explains about 4.8% of variance in business ratings, suggesting that these features aren't significantly influencing business ratings. 

## Final Model
Our final model was a `RandomForestRegressor` used predict a business's average google Maps rating. We selected a Random Forest Regressor as it will be able nonlinear relationships between introduced feature that baseline model may miss.

**Additional Features:**

- `zipcode` - We wanted to use zipcode since it captures geographic information about business location, which could be associated with customer demographics and business environment. More specifically, some parts of the island might be regarded as more “luxurious” or “fancy,” which may be associated with higher average business ratings if customers rate businesses in those areas more highly than others. Therefore, zipcode may have an association with `avg_rating`, which can help our model performance by adding location based information that was not captured by `price` or `num_of_reviews` alone.
- `description_length` - Businesses with longer descriptions may prioritize providing customers with more detailed information which may highlight their attention to detail. A longer description could also suggest that a business is more established which may also be associated with higher average ratings. Therefore, `description_length` may help improve model performance by capturing additional information about how much effort a business puts in which may influence the `avg_rating`.

Final model uses 4 features:
- `price` (ordinal)
- `num_of_reviews` (quantitative)
- `zipcode` (nominal)
- `description_length` (quantitative)

**Feature Transformations:**

Since `price` is an ordinal categorical variable, it was encoded using `OrdinalEncoder` with the following ordering: `$ < $$ < $$$ < $$$$`

A logarithmic tranformation (`log1p`) was applied to `num_of_reviews` to account for large differences in review counts across a diverse business landscape.

`zipcode` was encoded using `OneHotEncoder` because zip codes are nominal categories without orderings.

`description_length` is a numerical feature that counts words in description.

Preprocessing and fitting of model was implemented in sklearn pipeline

To create best model, we performed `GridSearchV` over Random Forest hyperparameters. In specific, we tuned the following hyperparameters to the best-performing in cross-validation performance:
- `n_estimators` (# of trees) = 10
- `max_depth`(max depth of each tree) = 300
  
**Model Performance:**
- R²: 0.055

  The final model slighlty improved upon baseline by incorporating `zipcode` and `description_length`, the model explaining a greater proportion of variance. Nonetheless, improvement was limited, suggesting that these additional features dont influence business ratings much.
  

## Fairness Analysis

To evaluate model performance across groups, we will compare businesses with relatively low reviews agaisnt businesses with many reviews.

**Groups:**
- Group X: Low-review businesses (below the median # of reviews)
- *Group Y: High-review businesses (at or above median # of reviews

**Evaulation Metric:** Because our prediction problem is a regression question, we will utilize `R²` as our evaluation metric.

**Hypotheses:** 

**Null -** The model is fair, any difference in `R²` between low/high review businesses is random chance.

**Alternate -** The model is unfair, model achieves higher `R²` for high review business than low review business.

**Test Statistic:** R²(high-review businesses) - R²(low_review businesses)
Large positive values provide evidence for alternate

**Significance Level:** α = 0.05

**Results:**

- Observed Statistic: 0.04718911054922892
- P-value: 0.004995004995004995

**Conclusion:** 0.004995004995004995 < .05, we reject the null. There is evidence that model performs better for high-review businneses.


