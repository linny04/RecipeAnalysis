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

#### `n_steps` and `rating`

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


#### `minutes` and `rating`

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


---
