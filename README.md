# Investigation on the Relationship Between Amount of Protein and Time to make Recipes

by Matthew Lin

---

## Introduction

A lot of people look online for new recipe to make everyday and want to find healthy meals that provide them with the right nutrition. While there are many of us who love consuming tasty food, many also find that it is important to consume the right foods that are tailored to their diets. Protein is important for muscle gains and also retention of muscle mass in the body. Knowing that it is hard to find the balance in everyday life for finding a good time to cook or not having enough time in general, we wonder **if recipes that take longer to prepare and make are less dense in nutrients, specifically protein.** We will analuze two datasets that consists of recipe and ratings from [food.com](https://www.food.com/).

Our `recipe` dataset contains 83782 rows with 12 columns:

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

Our `interactions` dataset includes 731927 rows and 5 columns:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date of interaction |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

---

## Cleaning and EDA

We will first clean out dataset and add additional columns which would make it easier to perform our analysis.

1. First we will left merge the recipes and interactions datasets on id and recipe_id. This will give us a complete dataset with the ratings and review information in the same dataset as the recipe information.

2. Next we will fill all the ratings of 0 with np.nan. This is because the rating on [food.com](https://www.food.com/) using a scale from 1-5, meaning the lowest rating possible is 1 and the highest rating possible is 5. Ratings that are 0 are invalid therefore we will replace values of 0 with np.nan.

3. Next, we will add a column `average_rating` which is the average rating per recipe. We will create this column to find the average rating of each recipe since different users will have different ratings for each recipe.

4. Because we are analyzing the amount of protein in each recipe, we will create a new column called `protein` which extracts the daily PDV of protein in each recipe. Knowing that the nutrtion information in the `nutrtion` column is in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)], we can extract the protein PDV from this. We will also create another column using the same procedure called `calories`. After creating both of these new columns, we will convert these string objects to float values.

5. Next we will add a new column called `prop_protein` which is the proportion of protein of the total calories in the recipe. To calculate this, we will use the values in our `protein` column and divide to by 100 to get it in decimal form and then multiply it by 51 because 51 grams of protein is the 100% daily value (PDV). To convert this value into the number of calories from protein, we will multiply it by 4 since there are 4 calories in 1 gram of protein. After that, we will divide this number by the total amount of calories in the recipe to get the proportion of protein of the total calories.

6. We will create a new column called `time_cat` which categorizes the recipes based on the amount of minutes it takes to make it. We will categorize it into two groups: Easy-Medium and Long where the Easy-Medium is defined as recipes that take 60 minutes or less to make and Long recipes are recipes that take over 60 minutes.

7. We will create a new column called `'cal_cat'` which categorizes the recipes based on the amount of calories are in each recipe. We will categorize it into four groups: Low, Medium, High, and Very High where Low is defined as recipes with 300 calories or less, Medium is defined as recipes with more than 300 calories and 500 calories or less, High is defined as recipes with more than 500 calories and 700 calories or less, and Very High is defined as recipes with more than 700 calories.


**Final Dataset**

|  Column        | Description |
|:---------------|:---------|
| `'name'`           | object   |
| `'id'`             | int64    |
| `'minutes'`        | int64    |
| `'contributor_id'` | int64    |
| `'submitted'`      | object   |
| `'tags'`           | object   |
| `'nutrition'`      | object   |
| `'n_steps'`        | int64    |
| `'steps'`          | object   |
| `'description'`    | object   |
| `'ingredients'`    | object   |
| `'n_ingredients'`  | int64    |
| `'user_id'`        | float64  |
| `'recipe_id'`      | float64  |
| `'date'`           | object   |
| `'rating'`         | float64  |
| `'review'`         | object   |
| `'avg_rating'`     | float64  |
| `'protein'`        | float64  |
| `'calories'`       | float64  |
| `'prop_protein'`   | float64  |
| `'time_cat'`       | category |
| `'cal_cat'`        | category |




### Univariate Analysis

For the univariate analysis, I examined the distribution of the proportion of protein in each recipe. We can see that the distribution is right skewed telling us that most of the recipes have a lower proportion of protein. As the proportion of protein in a recipe increases, there are less of these recipes.


<iframe
  src="assets/distribution-of-prop-protein.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis

For the bivariate analysis, I examined the distrbution of the proportion or protein in each recipe categorized into two categories, Easy-Medium and Long recipes. We can see that the distribution for both categories are still right skewed but that there are a significantly more amount of Easy-Medium recipes. 

<iframe
  src="assets/distribution-of-prop-protein-time-cat.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates

For this section, I wanted to investigate the relationship between the number of steps and the proportion of protein in a recipe. I filtered first grouped the data into the number of steps that each recipe had and then calculated five different statistics: mean, median, minimum, maximum and the standard deviation. The pivot table that I created is shown below:

|   n_steps |   mean_prop |   median_prop |   min_prop |   max_prop |        std |
|----------:|------------:|--------------:|-----------:|-----------:|-----------:|
|         1 |   0.0919193 |     0.0560902 | 0          |  0.775297  | 0.118624   |
|         2 |   0.0973729 |     0.068533  | 0          |  0.765     | 0.107448   |
|         3 |   0.114684  |     0.0827586 | 0          |  0.764373  | 0.11726    |
|         4 |   0.138832  |     0.105209  | 0          |  0.848527  | 0.13137    |
|         5 |   0.157365  |     0.11564   | 0          |  0.885079  | 0.141856   |
|       ... |       ...   |     ...       | ...        |    ...     |    ...     |
|        87 |   0.131119  |     0.131119  | 0.131119   |  0.131119  | 0          |
|        88 |   0.30579   |     0.334949  | 0.0433581  |  0.334949  | 0.0922092  |
|        93 |   0.158303  |     0.158303  | 0.158303   |  0.158303  | 0          |
|        98 |   0.0200787 |     0.0200787 | 0.0200787  |  0.0200787 | 0          |
|       100 |   0.0698248 |     0.0698248 | 0.0698248  |  0.0698248 | 0          |

I then plotted the data on a line graph and the results that I found were interesting. There is a small trend that as the number of steps increase the maximum proportion of protein in a recipe starts to decrease slowly with fluctuating trend lines. The lines for the mean and median follow similar trends, spiking at the recipes that have the same number of steps. However, there is no clear trend suggesting that there might not be a linear relationship between the number of steps for a recipe and the proportion of protein.

<iframe
  src="assets/aggregate-plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>



---

## Assessment of Missingness

### NMAR Analysis

There are a lot of missing values in the `description` column and I believe that this column is NMAR because if there are users who are less experienced and maybe posted an unfinished recipe, their post could be missing some information which could include the description. First time users of the website could be inexperienced to using the website and might forget to add a description or feel like thier receipe is not good enough to add a description. Recipes who have a description usually are higher quality recipes that most people might find confusing just by reading the title or name of the recipe, requiring a description. For recipes that are more self explanatory and more simple like cookies or cupcakes, the creators of the recipe might not include a description as they might believe that there is no need for a description.


### Missingness Dependency

There were a lot of missing values in our `ratings` column and we wanted to test out to see if the missingness depended on two other columns: `n_steps` which is the number of steps in the recipe and `minutes` which is the amount of time it takes to make the recipes in minutes. 

### `n_steps` and `rating`

**Null Hypothesis:** The missingness of ratings does not depend on the number of steps in the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the number of steps in the recipe.

**Test Statistic:** The absolute difference of mean in the number of steps of the distribution of the group without missing ratings and the distribution of the group with missing ratings.

**Signficance level (alpha):** 0.05

<iframe
  src="assets/distribution-nsteps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/emp-distribution-nsteps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

To test to see if the missingness of ratings depends on the number of steps in the recipe, we ran a permutation test where we shuffled the missingness of rating 1000 times. By doing this, we create 1000 diffrent simulations and we collected 1000 absolute difference of means in the two distributions. 

Before running the permutation test, we found the **observed statistic**, the absolute difference of mean between the two distributions, to be **1.3386**. Using the observed test statistic we found, we found our **p-value** to be **0.0000** and at the 0.05 significance level, we **reject the null hypothesis** since 0.0000 < 0.05. The missingness of ratings does depend on the number of steps in each recipe. 


### `minutes` and `rating`

**Null Hypothesis:** The missingness of ratings does not depend on the time it takes to make the recipe.

**Alternate Hypothesis:** The missingness of ratings does depend on the time it takes to make the recipe.

**Test Statistic:** The absolute difference of mean in minutes of the distribution of the group without missing ratings and the distribution of the group with missing ratings.

**Signficance level (alpha):** 0.05

<iframe
  src="assets/distribution-minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/emp-distribution-minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

To test to see if the missingness of ratings depends on the times it takes to make the recipe, we ran a permutation test where we shuffled the missingness of rating 1000 times. By doing this, we create 1000 diffrent simulations and we collected 1000 absolute difference of means in the two distributions. 

Before running the permutation test, we found the **observed statistic**, the absolute difference of mean between the two distributions, to be **51.4524**. Using the observed test statistic we found, we found our **p-value** to be **0.1340** and at the 0.05 significance level, we **fail to reject the null hypothesis** since 0.1340 > 0.05. The missingness of ratings does not depend on the cooking time of each recipe. 

---

## Hypothesis Testing

We want to investage the relationship between the amount of protein `prop_protein` in a recipe and the time it takes to make the recipe. We decided to categorize recipes using the minutes it takes to make it, where recipes that take 60 minutes or less are categorized as 'Easy-Medium' and recipes that take 60 minutes or more are 'Long'. Using these two definitions, we decided to run a permutation test to see if the proportion of protein are the same between the two distributions, 'Easy-Medium' and 'Long'. 

**Null Hypothesis:** The proportion of protein in recipes is the same in 'Easy-Medium' and 'Long' recipes.

**Alternate Hypothesis:** The proportion of protein in recipes is lower in 'Easy-Medium' recipes than in 'Long' recipes. 

**Test Statistic:** The difference in mean between proportion of protein of 'Easy-Medium' and 'Long' recipes.

**Signficance level (alpha):** 0.05

We are using a permutation test because we want to compare the two samples, 'Easy-Medium' and 'Long' recipes. Because we do not have any prior information about the population of both samples, we can use a permutation test to check and see if the two distributions come from the same population or is one higher than the other. By using a permutation test, we can test to see if recipes that take less time to make have a lower proportion of protein in the recipe. We hypothesize this because easier and less time consuming recipes might use ingredients that don't require as much cooking or require dealing with raw meat, which would be the main source of protein.Fast-cook recipes often center on produce, grains, or convenience items (pre-cooked items) that are relatively low in protein per calorie whereas high‐protein proteins (steak, chicken, pork) usually need higher, longer heat (roasting, braising) to become palatable, so they appear more in 'Long' recipes.

For the test statistic, we decided to use the difference in mean between proportion of protein of 'Easy-Medium' and 'Long' recipes because we wanted to see if 'Easy-Medium' recipes had a lower proportion of protein making it a directional hypothesis. We are not just looking to see if the two populations are different, but we want to test to see if one is lower than the other. To run the test, we first split our dataset into 'Easy-Medium' and 'Long' and found that the **observed statistic** to be **-0.0266**. Then we ran our permutation test, shuffling the `time_cat` column 1000 times to collect 1000 simulation mean differences between the two categories. 

<iframe
  src="assets/emp-distribution-hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

#### Conclusion
The **p-value** that we found was **0.0000** which is significantly less than our significance level of 0.05 (0.0000 < 0.05) so we **reject our null hypothesis** that the proportion of protein in recipes is the same in 'Easy-Medium' and 'Long' recipes. The proportion of protein is not the same in all recipes and recipes that take less time to make tend to have a lower protein-calories ratio. 

---

## Framing a Prediction Model
We plan to predict the time category of a recipe: 'Easy-Medium' or 'Long' which would be a classification problem because we are using our `time_cat` variable which is a categorical variable. We will predict whether the recipe is a 'Easy-Medium' recipe (60 minutes or less) or a 'Long' recipe (more than 60 minutes). 

We chose to predict the `time_cat` because prep time is one of the first things a home cook checks, and being able to anticipate whether a recipe will be a moderate-effort ('Easy-Medium') dish versus a longer-cook one can drive meal planning.

To evalute our model, we will use the f1 score because our two classes could be unevenly distributed and may be unbalanced. We would want to ensures we penalize both false positives and false negatives because the accuracy of our model can be misleading if one of the classes dominates the other. At the time of the prediction, we will only know the columns in the rating dataset and all predictors are things you'd see on the recipe page before you ever start cooking. 

---

## Baseline Model
For our baseline model, we will use a random forest classifier and split the data points into training and test sets. This is because we want to train our model using data that we already known and have seen and the test the data on unseen data to see the accuracy of our model. 

We will use two features for our baseline model, `prop_protein`, a column containing quantitative numerical values and `cal_cat`, a column containing cateogrical values.

We will one hot encode our `cal_cat` column and drop one of the encoded columns. For our `prop_protein` column, we will apply a `RobustScaler` transformation because it centers and scales your data using robust statistics.

We found that our **F1 score** for this model was **0.89** and the F1 score for 'Easy-Medium' was **0.93** and 'Long' was **0.76**. Our model predicts better for 'Easy-Medium' recipes and was not as accurate when predicting recipes that were 'Long'. This could be because that there were more recipes that were considered 'Easy-Medium' than 'Long' recipes so our model was able to better predict the 'Easy-Medium' recipes with more data points.

---

## Final Model
For our final model, we will again use a random forest classifier and split the data points into training and test sets. We will use `prop_protein`, `cal_cat`, `n_steps`, and `n_ingredients`.

`prop_protein`

This column contains the quantative numerical values that is the proportion of proteins in each recipe in relation to the number of calories. We decided to use a `RobustScaler` transformation for this column because it can sometimes have extreme outliers (e.g. an extreme protein‐dense recipe or one with zero calories leading to NaN). By using this transformation, we are centering the values by subtracting the median and then dividing it by the IQR. This helps improve our model because it can help prevent extremely large or small raw protein‐proportion values from altering other features during our calculations and reduce the bias. It also helps make our model more stable as the features' ranges become more comparable and not as wide across all our inputs. We are including this feature in our model because from our hypothesis test, we saw that recipes that were 'Easy-Medium' had a lower proportion of protein while 'Long' recipes had a higher proportion.

`cal_cat`

This column contains categorical values that categorizes the recipes based on the amount of calories are in each recipe. We categorized it into four groups: Low, Medium, High, and Very High where Low is defined as recipes with 300 calories or less, Medium is defined as recipes with more than 300 calories and 500 calories or less, High is defined as recipes with more than 500 calories and 700 calories or less, and Very High is defined as recipes with more than 700 calories. We decided to one hot encode this column and drop one of the encoded columns. We chose this feature becaused recipes that have higher calories will have require more steps and time to make because of the increase in ingredients. With more calories, we are including more 'ingredients' and are more likely to have more steps since it is a more complex recipe.

`n_steps`

We decided to include `n_steps` which is a numerical column that contains integer values of the number of steps for each recipe. We decided to include this feature in our model because recipes that take longer to make (minutes) usually have more steps. We decided to use a `Binarizer` transformation on this feature, using the median as the threshold. By using this transformation, it helps reduce the noise from minor differences in our data. It creates a more broad definition for the column and allows for a more clear breakpoint between the two groups 

`n_ingredients`

We decided to include `n_ingredients` which is a numerical column that contains integer values of the number of ingredients for each recipe. Similar to `n_steps`, we will include this feature in our model because recipes that take longer to make (minutes) usually include more ingredients. We decided to use a `Binarizer` transformation on this feature, using the median as the threshold. By using this transformation, it helps reduce the noise from minor differences in our data. It creates a more broad definition for the column and allows for a more clear breakpoint between the two groups.


We used RandomForestClassifier as our modeling algorithm and conducted GridSearchCV to tune the hyperparameters of max_depth and n_estimators of the RandomForestClassifier. We decided to use RandomForestClassifier because it is more robust to outliers when compared to a linear model because splitting on thresholds makes them inherently robust when a few recipes have extreme values. Using GridSearchCV, we were able to extract the most predictive combination of models and have the highest accuracy. 

The **F1 score** of our final model was **0.91**, which saw a 0.02 increase from our baseline model. The F1 score for both 'Easy-Medium' and 'Long' both increased to 0.94 and 0.81 respectively, with 'Long' increasing by 0.05. 

---

## Fairness Analysis
We decided to split the recipes into two groups by the number of ingredient in each recipe. We designed for our high ingredients group to be recipes with more than 9 ingredients and our low ingredients group to be recipes with 9 or less ingredients. We found that the median number of ingredients for our recipe dataset was 9 which is why we chose this as our threshold. We chose to use the median to binarize our dataset because of the robustness to outliers. If we used the mean, a few recipes with extremely large outliers could pull the mean upward which would cause our data to be skewed. The median will center our distribution and will create balanced group sizes. This way we will have exactly half or close to half of the recipes end up in "low" and half in "high". This helps us balance the model when fitting it onto our prediction model.

**Null Hypothesis**: Our model is fair. Its precision for recipes with higher number of ingredients and lower number of ingredients are roughly the same.

**Alternative Hypothesis**: Our model is unfair. Its precision for recipes with lower number of ingredients is lower than its precision for recipes with higher number of ingredients.

**Test Statistic**: Difference in precision between recipes with higher number of ingredients and lower number of ingredients.

**Signficance Level**: 0.05

Before running the permutation test, we got an observed test statistic of **0.0137**. We then ran a permutation test and collecting all the sample test statistics. After running the permutation test, we found a p-value of **0.9740** and since the p-value is greater than our signifiance level of 0.05, we fail to reject the null hypothesis that our model is fair. The model's precision for recipes with less ingredients is the same as its precision for recipes with a higher number of ingredients.