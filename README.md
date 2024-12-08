# Taste or Waste?

#### Authors: Christopher Shang, In Lorthongpanich

## Introduction

As society moves in the direction of physical fitness, understanding how different factors influence the popularity of dishes can uncover how to promote healthier ones. The question we try to answer is how nutrition and effort affect the popularity of recipes. We used the **“Recipes and Ratings”** dataset, which originally contains 234,429 rows after merging the `recipes` and `interactions` datasets on `recipe_id`. The relevant names of the columns are: `name`, `id`, `minutes`, `n_steps`, `n_ingredients`, `rating`, `nutrition`, and `tags`. `name` describes the name of the recipe, which aren’t all unique, which is why `id` is important in separating recipes for analysis. `minutes` is how long the recipe takes to make, with `n_steps` and `n_ingredients` representing the required number of steps and ingredients. `rating` is the rating that each individual reviewer left on each recipe, so each recipe has multiple ratings. `nutrition` and `tags` are columns of lists, where `nutrition` includes the calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV), and `tags` includes many different shared identifiers such as the meal of the day and other groups.

### Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Before cleaning, we performed some analysis of graphing the distributions of a few of the important columns, such as ratings, to learn about any outliers and interesting skews. We found that there were some outrageous outliers in calories and minutes due to recipes that took months to distill or recipes for a huge part of people. Because of this, we decided to filter the dataset to only include recipes that took under 10,000 minutes with under 2,000 calories, which we deemed reasonable after looking at the distributions again. Additionally, we expanded `nutrition` and `tags`. We turned each element in the nutrition list into its own column and turned tags into a categorical column that only included the type of `meal` a recipe was (breakfast, lunch, dinner, dessert) in order to one hot encode later. 

We filled all ratings of 0 with `np.nan` because a rating of 0 likely represents missing or invalid data rather than an actual user evaluation, as ratings typically range from 1 to 5. We also decided to drop all rows that had NaN ratings, since when we are trying to predict ratings these will provide no additional information, and the distribution of the NaN ratings matches the distribution of the overall dataset, so no significant skews will result from just dropping them. Finally, we decided to drop all other irrelevant columns for our prediction, including `id`, `nutrition`, `steps`, `description`, `ingredients`, and `review`, just to make the table cleaner to look at.

| name                                    | meal      |   n_ingredients |   n_steps |   minutes |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |   rating |   avg_rating |
|:----------------------------------------|:----------|----------------:|----------:|----------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|---------:|-------------:|
| 1 brownies in the world    best ever    | lunch     |               9 |        10 |        40 |      138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |        4 |         4    |
| millionaire pound cake                  | dinner    |               7 |         7 |       120 |      878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 |        5 |         5    |
| 5 tacos                                 | dinner    |               9 |         5 |        20 |      249.4 |                26 |             4 |              6 |              39 |                    39 |                     0 |        4 |         4    |
| blepandekager   danish   apple pancakes | breakfast |              10 |        10 |        50 |      358.2 |                30 |            62 |             14 |              19 |                    54 |                    12 |        5 |         5    |
| bbq spray recipe    it really works     | breakfast |               3 |         5 |         5 |       47.2 |                 0 |             2 |              0 |               0 |                     0 |                     0 |        5 |         4.75 |

### Univariate Analysis

We first investigated the overall distribution of the `rating` column that we are trying to predict and noticed that there are an overwhelming majority of `5`s. This will be important when we consider how our model is being trained, since we might be oversampling fives and that assessing accuracy will be interesting with such little representation from other ratings.

<iframe
  src="assets/indiv-rating-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We also plotted the distribution of `meal`, and the trend shows that the most common type of recipe is for dinner dishes, followed by dessert, lunch, and breakfast, respectively. The good thing is that there is a good amount of data for all meals, and these are mutually exclusive categories that will hopefully help us better predict ratings, as it factors in a new factor of what time of day the food is being eaten.

<iframe
  src="assets/meal-bar.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

We plotted `minutes` against `rating` to see if there were any interesting insights, as intuition leads us to believe that some people may prefer shorter and easier recipes to longer and harder ones. However, the graphs show us that there is minimal difference between how long the recipes take and the average ratings they end up receiving, which will be interesting to see if this feature plays a good role in predicting in the final model. There is some slight variation, but since fives were such a huge part of the dataset, it might make sense that most averages are high.

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

A similar trend exists in the relationship between `calories` and average rating (`avg_rating`), where the distribution looks almost completely evenly distributed. This presents a similar problem to investigate as we try to predict ratings.

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

Finally, we plotted the number of ingredients (`n_ingredients`) against the average ratings (`avg_rating`), with a similar trend as the other two relationships. 

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

This grouped table shows the relationship between the number of ingredients (`n_ingredients`) and the average of the nutrional value of each ingredient. It can be interesting to see that the number of ingredients seems to be positively correlated to calories and many of the other nutritional metrics like fat, sugar, and carbs. However, one that stands out is protein, which follows a more quadratic relationship. This also shows that protein is likely the only nutrition that is linearly independent from calories, which makes sense from general food knowledge since fat, sugar, and carbs directly affect calories while protein is more separate.

Top 7 rows sorted by calories:

|   n_ingredients |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
|               3 |    248.927 |           16.4457 |       65.4795 |        21.4057 |         13.9062 |               21.7418 |               9.22445 |
|               2 |    262.96  |           17.1529 |       67.6815 |        45.1688 |         14.8981 |               21.3057 |              10.1115  |
|               4 |    278.519 |           20.0597 |       61.0194 |        17.6704 |         18.2671 |               27.1001 |               9.34015 |
|               5 |    294.87  |           21.6144 |       59.1062 |        20.4802 |         20.3656 |               28.4102 |               9.88809 |
|               6 |    314.897 |           23.1842 |       54.5716 |        21.6814 |         23.1432 |               29.7676 |              10.2697  |
|              33 |    338.2   |           25      |       18      |        16      |          8      |               12      |              14       |
|               7 |    345.104 |           26.7115 |       49.8763 |        25.7251 |         27.6638 |               33.3561 |              10.4786  |

Bottom 7 rows sorted by calories:

|   n_ingredients |   calories |   total fat (PDV) |   sugar (PDV) |   sodium (PDV) |   protein (PDV) |   saturated fat (PDV) |   carbohydrates (PDV) |
|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|
|              26 |    629.591 |           51.0909 |       38.2727 |        50      |         59.8182 |               74.9091 |               17.6364 |
|              25 |    645.219 |           39.9375 |       54.375  |        70.6875 |         76.875  |               44.75   |               21.1875 |
|              30 |    666.383 |           58.5    |       39.5    |        42.5    |         60      |               66.8333 |               16.8333 |
|              27 |    719.288 |           51.375  |      183.875  |        48.5    |         50.5    |               69      |               27.375  |
|              29 |    827.4   |           55.5    |      121.333  |        44.6667 |         83      |               64      |               29.1667 |
|              28 |    842.614 |           48.8571 |      133.714  |       591.429  |         93      |               42.4286 |               34.1429 |
|              31 |   1184.37  |          114.667  |      346      |       181.667  |         38      |               83      |               37      |

### Imputation Strategies

Only the `rating` column contained any missing values, and we decided not to impute the missing values because the graph of the distribution of the average recipe ratings for each missing rating showed the same distribution as the distribution of just the total ratings. This tells us that simply dropping the ratings will not affect the distribution of our dataset, so we can safely do so to avoid any complexities.

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

Our prediction task is a multiclass classification problem, where we aim to predict the rating of a recipe (1-5) based on its characteristics, such as nutritional content, preparation time, and meal type. We chose `rating` as the response variable because it reflects recipe popularity and aligns with our goal of understanding how factors like nutrition and effort influence user preferences. The ratings are categorical integers, making classification more appropriate than regression, as it ensures predictions align with the actual classes.

To evaluate the model, we use the F1-score, which balances precision and recall, making it suitable for our imbalanced dataset where `rating = 5` dominates. Accuracy is not ideal in our case because it could overrepresent the majority class without reflecting model performance for minority classes. Our features include nutritional data (e.g., `calories`, `protein (PDV)`), `n_ingredients`, `minutes`, and `meal` type, as they are available at the time of prediction.

## Baseline Model

Our baseline model predicts the rating of a recipe (1-5) using five features: `calories`, `minutes`, `protein (PDV)`, `n_ingredients`, and `meal`. Among these features, four (`calories`, `minutes`, `protein (PDV)`, and `n_ingredients`) are quantitative, while `meal` is nominal. For preprocessing, we applied one-hot encoding to the `meal` column to transform it into binary indicators, while leaving the numerical features unchanged. All steps, including feature transformation and model training, were implemented in a single sklearn pipeline.

The baseline model uses a **Logistic Regression** classifier wrapped in `OneVsRestClassifier`, with `class_weight='balanced'` to address the imbalance in the dataset, where `rating = 5` dominates. On the test set, the model achieved an accuracy of 22%, with a weighted F1-score of **0.29.** While the model demonstrates some ability to predict the majority class (`rating = 5`), its performance on minority classes is poor, as evidenced by the low macro-average F1-score of 0.11. This indicates that the current model struggles to generalize to unseen data and handle the imbalanced dataset effectively. 

### Classification Report for Baseline Model
              precision    recall  f1-score   support

         1.0       0.01      0.50      0.03       252
         2.0       0.00      0.00      0.00       193
         3.0       0.00      0.00      0.00       560
         4.0       0.16      0.29      0.21      3193
         5.0       0.77      0.21      0.33     14500

    accuracy                           0.22     18698
    macro avg      0.19      0.20      0.11     18698
    weighted avg   0.63      0.22      0.29     18698

<iframe
  src="assets/base-confusion_matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Final Model

Our final model improves upon the baseline by incorporating feature engineering and a hyperparameter search. We engineered two new features using `PolynomialFeatures` for `calories` and `protein (PDV)` to capture non-linear relationships that may exist between these features and user ratings. Additionally, we applied transformations such as `log1p` and `sqrt` to the `calories` feature using a `FunctionTransformer`, which helps reduce the skewness in its distribution, making it more interpretable. These new features and transformations, combined with `StandardScaler` for the `minutes` column and `OneHotEncoder` for the `meal` column, were integrated into a single pipeline.

The model used for this pipeline was a **Logistic Regression** classifier wrapped in a `OneVsRestClassifier` to handle multiclass classification. To optimize the model's performance, we performed hyperparameter tuning using `GridSearchCV`, testing polynomial degrees (2–4), different transformations (`log1p` and `sqrt`), and regularization strengths (`C`) for the Logistic Regression classifier. The best-performing model used a polynomial degree of 2, a square root transformation (`sqrt`) for `calories`, and a `C` value of 0.1 for regularization. These parameters acheived a balance between capturing complex relationships of recipes and ratings, while preventing overfitting.

The final model showed minor improvements over the baseline. The baseline model achieved an accuracy of 22% and a weighted F1-score of 0.29. In contrast, the final model achieved an accuracy of 29% and a weighted F1-score of **0.36**. The macro-average F1-score also improved alightly, indicating better performance across all classes, including minority ones. This improvement is attributed to the additional feature engineering, which allowed the model to better represent the data, and the hyperparameter tuning, which optimized the model for the given task.

### Classification Report for Final Model
              precision    recall  f1-score   support

         1.0       0.02      0.26      0.03       252
         2.0       0.01      0.21      0.03       193
         3.0       0.04      0.04      0.04       560
         4.0       0.19      0.37      0.26      3193
         5.0       0.79      0.28      0.41     14500

    accuracy                           0.29     18698
    macro avg      0.21      0.23      0.15     18698
    weighted avg   0.65      0.29      0.36     18698


<iframe
  src="assets/final-confusion_matrix.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Conclusion

From our exploration of the **Recipe and Ratings** dataset, it can be seen that there is a very low correlation between the nutritional value of a food and the ratings it receives on a recipe website. When running our model to predict the ratings from a variety of factors including calories, number of ingredients, protein, our final model was only able to get a weight F-score of **0.36**. When referring back to the distribution of ratings and its relationship between these features, it is not a surprise that they have a hard time accurately predicting since the original data is so skewed and they had limited correlations. While we were unsuccessful in creating a highly accurate model to predict ratings from nutrition, the data can still be useful in hinting that good and bad nutrition have a similar popularity, which is a good sign for the future of the health and fitness industry. For the next steps of the research, it would be interesting to create predictions from a more evenly distributed dataset of ratings to see if it would make a difference from how we handled the uneven distribution (using weights). Additionally, finding a metric for taste would be interesting to see how that factor might affect ratings more than the nutritional content of the food.
