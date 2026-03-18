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

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
