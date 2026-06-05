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

## Baseline Model

## Final Model

## Fairness Analysis
