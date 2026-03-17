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

We use these two datasets to create one merged dataset, merging on recipe to ensure that we only include a row as long as we have a corresponding recipe for it. This also means that we can have rows with the same recipe but different review. Additionally while merging we add a column containing the average rating for each recipe.

| Column        | Description         |
| :------------ | :------------------ |
| `'average_rating'`   | Average Rating of recipe             |


# Data Cleaning and Exploratory Data Analysis

# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
