# Taste or Waste?

#### Authors: Christopher Shang, In Lorthongpanich

## Introduction

As society moves in the direction of physical fitness, understanding how different factors influence the popularity of dishes can uncover how to promote healthier ones. The question we try to answer is how nutrition and effort affect the popularity of recipes. We used the **“Recipes and Ratings”** dataset, which originally contains 234,429 rows after merging the recipes and ratings datasets on recipe ID. The relevant names of the columns are: name, id, minutes, n_steps, n_ingredients, rating, nutrition, and tags. Name describes the name of the recipe, which aren’t all unique, which is why id is important in separating recipes for analysis. Minutes is how long the recipe takes to make, with n_steps and n_ingredients representing the required number of steps and ingredients. Rating is the rating that each individual reviewer left on each recipe, so each recipe has multiple ratings. Nutrition and tag are columns of lists, where nutrition includes the calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV), and tag includes many different shared identifiers such as the meal of the day and other groups.

### Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Before cleaning, we performed some analysis of graphing the distributions of a few of the important columns, such as ratings, to learn about any outliers and interesting skews. We found that there were some outrageous outliers in calories and minutes due to recipes that took months to distill or recipes for a huge part of people. Because of this, we decided to filter the dataset to only include recipes that took under 10,000 minutes with under 2,000 calories, which we deemed reasonable after looking at the distributions again. Additionally, we expanded nutrition and tags. We turned each element in the nutrition list into its own column and turned tags into a categorical column that only included the type of meal a recipe was (breakfast, lunch, dinner, dessert) in order to one hot encode later. We also decided to drop all rows that had NaN ratings, since when we are trying to predict ratings these will provide no additional information, and the distribution of the NaN ratings matches the distribution of the overall dataset, so no significant skews will result from just dropping them. We also decided to drop all other irrelevant columns for our prediction including id, nutrition, steps, description, ingredients, and review, just to make the table cleaner to look at.

| name                                    | meal      |   n_ingredients |   n_steps |   minutes |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |   rating |   avg_rating |
|:----------------------------------------|:----------|----------------:|----------:|----------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|---------:|-------------:|
| 1 brownies in the world    best ever    | lunch     |               9 |        10 |        40 |      138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |        4 |         4    |
| millionaire pound cake                  | dinner    |               7 |         7 |       120 |      878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 |        5 |         5    |
| 5 tacos                                 | dinner    |               9 |         5 |        20 |      249.4 |                26 |             4 |              6 |              39 |                    39 |                     0 |        4 |         4    |
| blepandekager   danish   apple pancakes | breakfast |              10 |        10 |        50 |      358.2 |                30 |            62 |             14 |              19 |                    54 |                    12 |        5 |         5    |
| bbq spray recipe    it really works     | breakfast |               3 |         5 |         5 |       47.2 |                 0 |             2 |              0 |               0 |                     0 |                     0 |        5 |         4.75 |

### Univariate Analysis

We first investigated the overall distribution of the ratings column that we are trying to predict and noticed that there are an overwhelming majority of fives. This will be important when we consider how our model is being trained, since we might be oversampling fives and that assessing accuracy will be interesting with such little representation from other ratings.

<iframe
  src="assets/avg-rating-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We also plotted the distribution of meals, and the trend shows that the most common type of recipe is for dinner dishes, followed by dessert, lunch, and breakfast, respectively. The good thing is that there is a good amount of data for all meals, and these are mutually exclusive categories that will hopefully help us better predict ratings, as it factors in a new factor of what time of day the food is being eaten.

<iframe
  src="assets/meal-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

We plotted minutes against the rating to see if there were any interesting insights, as intuition leads us to believe that some people may prefer shorter and easier recipes to longer and harder ones. However, the graphs show us that there is minimal difference between how long the recipes take and the average ratings they end up receiving, which will be interesting to see if this feature plays a good role in predicting in the final model. There is some slight variation, but since fives were such a huge part of the dataset, it might make sense that most averages are high.

<iframe
  src="assets/rating-min-box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/avg-rating-min-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

A similar trend exists in the relationship between calories and average rating, where the distribution looks almost completely evenly distributed. This presents a similar problem to investigate as we try to predict ratings.

<iframe
  src="assets/rating-cal-box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/avg-rating-cal-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Finally, we plotted the number of ingredients against the average ratings, with a similar trend as the other two relationships. 

<iframe
  src="assets/rating-cal-box.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/avg-rating-cal-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

This grouped table shows the relationship between the number of ingredients and the average of the nutrional value of each ingredient. It can be interesting to see that the number of ingredients seems to be positively correlated to calories and many of the other nutritional metrics like fat, sugar, and carbs. However, one that stands out is protein, which follows a more quadratic relationship. This also shows that protein is likely the only nutrition that is linearly independent from calories, which makes sense from general food knowledge since fat, sugar, and carbs directly affect calories while protein is more separate.

Top 7 rows sorted by calories:

|   n_ingredients |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
|               3 |    233.665 |           15.1793 |       58.178  |        18.2704 |         13.6887 |               19.5422 |               8.70273 |
|               4 |    273.232 |           18.9911 |       61.2475 |        15.9101 |         17.4492 |               25.8134 |               9.67255 |
|               5 |    295.824 |           22.2068 |       50.082  |        21.3015 |         22.6777 |               28.013  |               9.25705 |
|               6 |    318.485 |           23.5727 |       52.7395 |        20.5398 |         24.9242 |               30.478  |              10.1535  |
|               7 |    337.541 |           25.8984 |       48.1734 |        24.5571 |         27.3403 |               32.613  |              10.3042  |
|              33 |    338.2   |           25      |       18      |        16      |          8      |               12      |              14       |
|               8 |    343.604 |           26.4764 |       49.6151 |        25.8371 |         28.3988 |               33.6221 |              10.3894  |

Bottom 7 rows sorted by calories:

|   n_ingredients |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
|              25 |    660.912 |           41.4706 |       52      |        70.7059 |         78.7647 |               47.8235 |               21.4118 |
|              22 |    701.343 |           61.3281 |       67.5312 |        37.0573 |         68.8385 |               85.5833 |               16.6667 |
|              30 |    836.654 |           71.8462 |       47.6923 |        54.1538 |         78.3077 |               88.9231 |               21.1538 |
|              26 |    861.319 |           50.8125 |       28.5208 |        58.5833 |        104.271  |               71.0208 |               29.6042 |
|              28 |    929.4   |           51.8824 |      139.059  |       654.176  |         95.5294 |               49.8824 |               42.2941 |
|              29 |    948.44  |           56.2    |      119.9    |        80.9    |         97.8    |               51.2    |               37.3    |
|              31 |   1397.75  |          151.667  |      345.5    |       181.667  |         37.6667 |               92.5    |               37.5    |

### Imputation Strategies

Only the ratings column contained any missing values, and we decided not to impute the missing values because the graph of the distribution of the average recipe ratings for each missing rating showed the same distribution as the distribution of just the total ratings. This tells us that simply dropping the ratings will not affect the distribution of our dataset, so we can safely do so to avoid any complexities.

<iframe
  src="assets/avg-rating-nan-hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/avg-rating-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Framing a Prediction Problem

Our prediction task is a multiclass classification problem, where we aim to predict the rating of a recipe (1-5) based on its characteristics, such as nutritional content, preparation time, and meal type. We chose rating as the response variable because it reflects recipe popularity and aligns with our goal of understanding how factors like nutrition and effort influence user preferences. The ratings are categorical integers, making classification more appropriate than regression, as it ensures predictions align with the actual classes.

To evaluate the model, we use the F1-score, which balances precision and recall, making it suitable for our imbalanced dataset where rating = 5 dominates. Accuracy is not ideal in our case because it could overrepresent the majority class without reflecting model performance for minority classes. Our features include calories, nutritional data (e.g., calories, protein (PDV)), minutes, and meal type, as they are available at the time of prediction.

## Baseline Model



## Final Model

## Conclusion

From our exploration of the Recipe and Reviews dataset, it can be seen that there is a very low correlation between the nutritional value of a food and the ratings it receives on a recipe website. When running our model to predict the ratings from a variety of factors including calories, number of ingredients, protein, our final model was only able to get an accuracy of around [percent] percent. When referring back to the distribution of ratings and its relationship between these features, it is not a surprise that they have a hard time accurately predicting since the original data is so skewed and they had limited correlations. While we were unsuccessful in creating a highly accurate model to predict ratings from nutrition, the data can still be useful in hinting that good and bad nutrition have a similar popularity, which is a good sign for the future of the health and fitness industry. For the next steps of the research, it would be interesting to create predictions from a more evenly distributed dataset of ratings to see if it would make a difference from how we handled the uneven distribution (using weights). Additionally, finding a metric for taste would be interesting to see how that factor might affect ratings more than the nutritional content of the food.
