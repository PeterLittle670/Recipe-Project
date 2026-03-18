<!-- This is a project for the class DSC 80 about analyzing a large dataset of recipes and building a predictive model to predict ratings. -->

# Overview
This is a data science project conducted at UCSD analyzing the relationship between the rating of a recipe and the proportion of salt, sugar, and fat, — the three components that 
make food hyper-palatable and especially tantalizing to our taste buds.

Author: Peter Little

# Introduction
Hyper-Palatable foods have been a particular topic of controversy and debate regarding the diabetes epidemic in the US and the rise of hyper-processed, engineered foods to give us extreme flavors to keep us hooked on our favorite snacks. This project aims to explore the power of a particular holy-trinity used to make foods super tasty, a certain mix of salt, sugar, and fat, and whether we can see the influence of this in other foods in the ratings of recipes.

Our first dataset `recipes`, has 83782 rows totalling to 83782 unique recipes, with 10 columns recording the following information:


| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | Recipe name                                                                                                                                                                                       |
| `'id'`             | Recipe ID                                                                                                                                                                                         |
| `'minutes'`        | Minutes to prepare recipe                                                                                                                                                                         |
| `'contributor_id'` | User ID who submitted this recipe                                                                                                                                                                 |
| `'submitted'`      | Date recipe was submitted                                                                                                                                                                         |
| `'tags'`           | Food.com tags for recipe                                                                                                                                                                          |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe                                                                                                                                                                         |
| `'steps'`          | Text for recipe steps, in order                                                                                                                                                                   |
| `'description'`    | User-provided description                                                                                                                                                                         |
| `'ingredients'`    | Text for recipe ingredients                                                                                                                                                                       |
| `'n_ingredients'`  | Number of ingredients in recipe                                                                                                                                                                   |

Our second dataset, `ratings`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

We use these two datasets to create one new dataset, `merged`, merging on recipe to ensure that we only include a row as long as we have a corresponding recipe for it. This also means that we can have rows with the same recipe but different review. Additionally we add a column containing the average rating for each recipe. To aid in analyzing nutrition facts, we also add a separate column for each value in nutrition, resulting in a column for `calories (#)`, `total fat (PDV)`, `sugar (PDV)`, etc. PDV, or percent daily value shows how much a nutrient in a serving of food contributes to a total daily diet. Afterwards we can add a `hyper_palatable` that contains whether a food is hyper-palatable or not, we calculate this by checking if the salt, fat, and sugar in a food is above the median for each of those columns. 

<!-- By gathering this data, we now have our relevant columns, salt, fat, sugar, ratings, average ratings. -->

| Column        | Description         |
| :------------ | :------------------ |
| `'average_rating'`   | Average Rating of recipe             |

# Data Cleaning and Exploratory Data Analysis

To aid in analysis, we conducted the following data cleaning steps.

1. Left merge the recipes and interactions datasets on id and recipe_id.

   - This step helps match the unique recipes with their rating and review.

1. Check data types of all the columns.

   - This step helps us evaluate what data cleaning steps are appropriate for the dataset and if we need to conduct data type conversion.
   - | Column             | Description |
     | :----------------- | :---------- |
     | `'name'`           | object      |
     | `'id'`             | int64       |
     | `'minutes'`        | int64       |
     | `'contributor_id'` | int64       |
     | `'submitted'`      | object      |
     | `'tags'`           | object      |
     | `'nutrition'`      | object      |
     | `'n_steps'`        | int64       |
     | `'steps'`          | object      |
     | `'description'`    | object      |
     | `'ingredients'`    | object      |
     | `'n_ingredients'`  | int64       |
     | `'user_id'`        | float64     |
     | `'recipe_id'`      | float64     |
     | `'date'`           | object      |
     | `'rating'`         | float64     |
     | `'review'`         | object      |

1. Fill all ratings of 0 with np.nan.

   - Rating is on a scale from 1 to 5, 1 meaning the lowest rating while 5 means the highest rating. With that being said, a rating of 0 indicates missing values in rating. Therefore we filled all values of 0 with np.nan.

1. Add column `'average_rating'` containing average rating per recipe.

   - Since a recipe can have numerous ratings from different users, we take an average of all the ratings to get a more comprehensive understanding of the rating of a given recipe.

1. Split values in the nutrition column to individual columns of floats.

   - Even though the values in the nutrition column look like lists, they're actually string objects. Given the description of the columns of the recipe dataset, we know what each individual value inside the brackets mean. Thus we can properly split up each value inside of the string and convert it into it's own column, giving us the columns `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, and `carbohydrates`.
   
1. Add `'hyper_palatable'` to the dataframe

   - `'hyper_palatable'` is a boolean column checking if the salt, fat, and sugar in a food is above the median for each column. This allows us to compare ratings between hyper-palatable foods and non-hyper-palatable foods.

### Result

| name                                 |     id |   rating |   average_rating |   calories |   total_fat |   sugar |   sodium | hyper_palatable |
|:-------------------------------------|-------:|---------:|-----------------:|-----------:|------------:|--------:|---------:|:----------------|
| 1 brownies in the world    best ever | 333281 |        4 |                4 |      138.4 |          10 |      50 |        3 | False           |
| 1 in canada chocolate chip cookies   | 453467 |        5 |                5 |      595.1 |          46 |     211 |       22 | True            |
| 412 broccoli casserole               | 306168 |        5 |                5 |      194.8 |          20 |       6 |       32 | False           |
| 412 broccoli casserole               | 306168 |        5 |                5 |      194.8 |          20 |       6 |       32 | False           |
| 412 broccoli casserole               | 306168 |        5 |                5 |      194.8 |          20 |       6 |       32 | False           |

## Univariate Analysis

For this analysis we analyzed the distribution of the ratings to better understand the general pattern of how people reviewed recipes. From this bar plot, we can tell that ratings are highly skewed towards 4s and 5s, suggesting possible difficulty down the line trying to differentiate which variables are most predictive of rating.

<iframe
  src="assets/ratings-barplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Bivariate Analysis

For this analysis we analyzed the distribution of ratings hyper-palatable and non-hyper-palatable foods to look for any patternes and differences between the two distributions. However, from the box plot we can tell that their distributions are nearly identical with medians arounds 4.75 and similar interquartile ranges. This plot suggests that whether a food is hyper-palatable or not has little discernible effect on it's rating, likely due to the ceiling effect observed in the univariate analysis above.

<iframe
  src="assets/ratings-vs-hyper-palatable-boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Interesting Aggregates

The pivot table below shows the average recipe rating broken down by calorie range 
and hyper-palatable status. Across all calorie ranges, ratings remain consistently 
high around 4.6 for both hyper-palatable and non-hyper-palatable recipes, 
with no meaningful difference between the two groups. This further reinforces the 
ceiling effect observed in our univariate analysis, nutrition facts appear to 
have little influence on a recipe's rating regardless of calorie count.


| Calorie Range   |   Non-Hyper-Palatable |   Hyper-Palatable |
|:----------------|----------------------:|------------------:|
| 0-200           |                4.6844 |            4.5761 |
| 200-400         |                4.682  |            4.6927 |
| 400-600         |                4.6646 |            4.6733 |
| 600-800         |                4.6631 |            4.6792 |
| 800-1000        |                4.6139 |            4.6927 |
| 1000+           |                4.6593 |            4.6479 |


<!-- -Merging and filling in a ratings of zero with np.nan:
After merging we also fill in all ratings of 0 with `np.nan` because on our scale the only ratings you can give are from 1-5. -->

<!-- -Adding average rating
-Convert to String and Adding Nutrition Columns
-Add Hyper-Palatable Column -->

# Assessment of Missingness

## MNAR Analysis

We believe the `description` column is **MNAR** because a contributor who submitted a low-effort or simple recipe may have skipped writing a description because they felt the recipe didn't need one, therefore making the missingness of the recipe related to the quality or complexity of the recipe itself, which is unobserved. To make this MAR, we would want data such as the contributor's activity level or number of recipes submitted, which might explain the missing descriptions independent of recipe content.

## Missingness Dependency

We analyzed the missingness of the `description` column (114 missing values) by 
performing permutation tests against four columns: `n_steps`, `minutes`, 
`n_ingredients`, and `calories`. We used the KS statistic as our test statistic 
since it effectively compares two continuous distributions without assuming normality.

Our results were as follows:

| Column | KS Statistic | P-Value | Dependent? |
|--------|-------------|---------|------------|
| n_steps | 0.1335 | 0.0130 | Yes |
| minutes | 0.0940 | 0.1770 | No |
| n_ingredients | 0.1496 | 0.0040 | Yes |
| calories | 0.1583 | 0.0030 | Yes |

At a significance level of 0.05, the missingness of `description` depends on 
`n_steps`, `n_ingredients`, and `calories`, but does not depend on `minutes`. 

This makes intuitive sense from the data generating process: contributors who 
submitted more complex recipes with more steps, more ingredients, and higher 
calorie counts were more likely to invest the effort to write a description. 
In contrast, cooking time (`minutes`) has no meaningful relationship with whether 
a description was written — a recipe can take a long time without being complex 
(e.g. a slow cooker recipe with 3 ingredients that simply sits for 8 hours).

The missingness of `description` is therefore **MAR** (Missing At Random), dependent 
on observable columns in our dataset rather than on the description's content itself.

<iframe
  src="assets/desc-vs-num-ingredients-ks.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot above shows the empirical distribution of the KS statistic from our 
permutation test for `n_ingredients`. The observed KS statistic of 0.1496 (red 
dashed line) falls far in the tail of the permutation distribution, yielding a 
p-value of 0.004 and confirming that the missingness of `description` is 
significantly dependent on the number of ingredients in a recipe.

# Hypothesis Testing

## Question
Do hyper-palatable recipes (those above the median in fat, sugar, and sodium 
simultaneously) receive higher average ratings than non-hyper-palatable recipes?

## Hypotheses
**Null Hypothesis:** Hyper-palatable recipes have the same average rating as 
non-hyper-palatable recipes. Any observed difference is due to random chance.

**Alternative Hypothesis:** Hyper-palatable recipes have a higher average rating 
than non-hyper-palatable recipes.

## Test Statistic & Significance Level
We used the **difference in mean ratings** (hyper-palatable minus non-hyper-palatable) 
as our test statistic. This is a natural choice for comparing two groups on a 
continuous variable, and is easily interpretable — a positive value means 
hyper-palatable recipes rate higher on average. We set our significance level 
at **0.05**.

## Results
Our permutation test with 1,000 permutations yielded an observed difference of 
**-0.0034** and a p-value of **0.903**.

<iframe
  src="assets/hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Conclusion
At a significance level of 0.05, we **fail to reject the null hypothesis**. The 
observed difference in mean ratings of -0.0034 is well within the range of 
differences we would expect by random chance alone, as evidenced by the high 
p-value of 0.903. We cannot conclude that hyper-palatable recipes receive higher 
ratings than other recipes.

This result is likely influenced by the ceiling effect observed in our univariate 
analysis — ratings are so heavily skewed toward 4s and 5s across all recipes that 
there is little room for hyper-palatability to produce a detectable difference. 
This does not prove that hyper-palatability has no effect on ratings, but rather 
that any such effect is not detectable given the distribution of ratings in this 
dataset.

# Framing a Prediction Problem

### Prediction Problem
Can we predict the average rating of a recipe based on its nutritional content 
and other recipe properties available at the time of submission?

### Type
This is a **regression** problem, as we are predicting `average_rating`, a 
continuous variable ranging from 1 to 5.

### Response Variable
Our response variable is `average_rating`. We chose this variable because it is 
the most direct measure of how well a recipe is received by the Food.com community, 
and it naturally extends our hypothesis testing question — having found that 
hyper-palatability alone does not predict ratings, we now ask whether a broader 
set of nutritional and recipe features can do so.

### Evaluation Metric
We use **RMSE (Root Mean Squared Error)** to evaluate our model. RMSE is 
appropriate here because:
- It penalizes larger errors more heavily than MAE, which is desirable since 
  a prediction of 1 star when the true rating is 5 stars is much worse than 
  being off by 0.5 stars.
- It is interpretable in the same units as our response variable (stars), 
  making it easy to communicate model performance.
- R² was considered but is less intuitive for communicating prediction error 
  to a general audience.

### Justification of Features at Time of Prediction
All features we use are available at the time a recipe is submitted to Food.com — 
nutritional content, number of steps, number of ingredients, cooking time, and 
ingredients are all properties of the recipe itself, not derived from user 
interactions after the fact. We are careful to exclude any features derived 
from user reviews or interactions (such as number of reviews), as these would 
not be known at the time of prediction.

# Baseline Model

# Final Model

# Fairness Analysis
