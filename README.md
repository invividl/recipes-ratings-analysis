# Recipes and Ratings Analysis

### In Lorthongpanich, Christopher Shang

| name                                    |   minutes |   n_steps |   n_ingredients |   rating |   avg_rating |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) | meal      |
|:----------------------------------------|----------:|----------:|----------------:|---------:|-------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|:----------|
| 1 brownies in the world    best ever    |        40 |        10 |               9 |        4 |         4    |      138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 | lunch     |
| millionaire pound cake                  |       120 |         7 |               7 |        5 |         5    |      878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 | dinner    |
| 5 tacos                                 |        20 |         5 |               9 |        4 |         4    |      249.4 |                26 |             4 |              6 |              39 |                    39 |                     0 | dinner    |
| blepandekager   danish   apple pancakes |        50 |        10 |              10 |        5 |         5    |      358.2 |                30 |            62 |             14 |              19 |                    54 |                    12 | breakfast |
| bbq spray recipe    it really works     |         5 |         5 |               3 |        5 |         4.75 |       47.2 |                 0 |             2 |              0 |               0 |                     0 |                     0 | breakfast |

## Introduction

As society moves in the direction of physical fitness, understanding how different factors influence the popularity of dishes can uncover how to promote healthier ones. The question we try to answer is how nutrition and effort affect the popularity of recipes. We used the “Recipes and Ratings” dataset, which originally contains 234429 rows after merging the recipes and ratings datasets on recipe ID. The relevant names of the columns are: name, id, minutes, n_steps, n_ingredients, rating, nutrition, and tags. Name describes the name of the recipe, which aren’t all unique, which is why id is important in separating recipes for analysis. Minutes is how long the recipe takes to make, with n_steps and n_ingredients representing the required number of steps and ingredients. Rating is the rating that each individual reviewer left on each recipe, so each recipe has multiple ratings. Nutrition and tag are columns of lists, where nutrition includes the calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV), and tag includes many different shared identifiers such as the meal of the day and other groups.

### Data Cleaning

Before cleaning, we performed some analysis of graphing the distributions of a few of the important columns, such as ratings, to learn about any outliers and interesting skews. We found that there were some outrageous outliers in calories and minutes due to recipes that took months to distill or recipes for a huge part of people. Because of this, we decided to filter the dataset to only include recipes that took under 10,000 minutes with under 2,000 calories, which we deemed reasonable after looking at the distributions again. Additionally, we expanded nutrition and tags. We turned each element in the nutrition list into its own column and turned tags into a categorical column that only included the type of meal a recipe was (breakfast, lunch, dinner, other) in order to one hot encode later. We also decided to drop all rows that had NaN ratings, since when we are trying to predict ratings these will provide no additional information, and the distribution of the NaN ratings matches the distribution of the overall dataset, so no significant skews will result from just dropping them. We also decided to drop all other irrelevant columns for our prediction including id, nutrition, steps, description, ingredients, and review, just to make the table cleaner to look at.

### Data Cleaning and Exploratory Data Analysis

### Univariate Analysis

We first investigated the overall distribution of the ratings column that we are trying to predict and noticed that there are an overwhelming majority of fives. This will be important when we consider how our model is being trained, since we might be oversampling fives.

<iframe
  src="assets/avg-rating-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/meal-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis



### Interesting Aggregates

### Imputation Strategies

## Framing a Prediction Problem

## Baseline Model

## Final Model
