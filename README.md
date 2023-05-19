## Intoduction and Question Identification
We originally started with two datasets. The first was a dataset containing food information 
about many recipes. For example, some columns were: the name of the recipe, when it was
posted, its nutrition facts, the steps of the recipe, ingredients, etc. The second
dataset contains a recipe id that matches to an id in first dataset, along with reviews
for that particular recipe and the rating the person gave that recipe, among other columns.
In order to get started, we merged these datasets into one big dataset with 234,429
rows that contained information about each recipe.

Here is a more detailed explanation of the most relevant columns in the merged dataset:
-   `name` : name of the recipe
-   `minutes` : amount of minutes required to make the recipe
-   `nutrition` : nutrition facts in the form [calories (#), total fat (PDV - percent daily value), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]
-   `n_steps` : number of steps in the recipe
-   `steps` : list of steps in the recipe
-   `rating` : rating given to the recipe by a user
-   `id` : recipe ID

A question that we explored throughout this project was *"What factors influence the amount of calories in a recipe (SUGAR, ratings, etc)?"* <br>
We explored *"Do recipes with sugar tend to have more calories than recipes without sugar?"* in the HYPOTHESIS TEST portion.

---

## Cleaning and EDA (Exploratory Data Analysis)
#### Cleaning
As mentioned before, we started by merging the two datasets. These are the next steps we followed:
1. We filled all the ratings that had a value of 0 with `np.nan`. We did this because the rating
scale was from 1-5, so a rating of `0` is not possible. Thus, we opted that since 0 can still have
mathematical meaning in terms of a rating, we should replace it.
2. Since we saw that recipes had ratings/reviews from different users, we decided to
compute the average rating per recipe. We then added this information to our dataframe so
that each recipe had an average rating. We also dropped all duplicate recipes in our dataset
since the only differing information between recipes that appeared most than once were the reviews given by each user - we would not be 
using that info. This left us with 83782 rows.
3. In order to prepare for research question, we extracted the calorie value for each recipe from the 
`nutrition` column.
4. We then dropped all unnecessary columns. These were `['contributor_id', 'submitted', 'tags', 'steps', 'description', 'user_id', 'recipe_id', 'date', 'review', 'rating_x', 'nutrition']`. We were able to look at these columns and figure out that they would not have
any significance in our future analysis. 
5. We then noticed that there were pretty large outliers in the `minutes` and `calorie` columns.
Some recipes had calorie counts that were in the 20,000's and some recipes were taking almost 300
days. We decided to filter out these recipes because a recipe having that many calories and
taking that long is unfeasible and is probably due to input errors in the data gathering
process. We did not want these values to skew our analysis because we would be working with
means in our statistical analysis and outliers can heavily skew such a measure. Even though some values weren't of the
magnitude mentioned earlier, we wanted to choose the data that had most meaning to us. We
figured to keep only recipes with a calorie limit of 2350 (the mean of the 2200 and 2500 calories recommended for
women and men respectively). We reasoned the daily calorie limit because we figured a day of eating to consist
of 3 meals and a couple snacks so ~4 meals. A recipe that makes more than 4 meals at once was not as
interesting to us because we wanted to work with recipes that we could encounter in our actual lives.
As for minutes, we set a minute limit of 2880 which is 1.5 days. After this cleaning process, we were left with 82472 
(around 98.5% of the data from step 2).

<br>

Here is the first 5 rows of our resulting dataframe (note that for visual purposes, the ingredients column is shortened with ellipses):

| name                                  |     id |   minutes |   n_steps | ingredients                                                                                                                            |   n_ingredients |   Average Rating |   calories |
|:--------------------------------------|-------:|----------:|----------:|:---------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|-----------:|
| impossible macaroni and cheese pie    | 275022 |        50 |        11 | ['cheddar cheese',...,'red pepper sauce']                                                 |               7 |                3 |      386.1 |
| impossible rhubarb pie                | 275024 |        55 |         6 | ['cream cheese',...,'milk']                                                          |               8 |                3 |      377.1 |
| impossible seafood pie                | 275026 |        45 |         7 | ['frozen crabmeat',...,'nutmeg']                     |               9 |                3 |      326.6 |
| paula deen s caramel apple cheesecake | 275030 |        45 |        11 | ['apple pie filling',...,'pecans'] |               9 |                5 |      577.7 |
| midori poached pears                  | 275032 |        25 |         8 | ['midori melon liqueur',...,'mint']        |               9 |                5 |      386.9 |

<br>

#### Variate Analysis of Calories

This is the distribution of the `calorie` column:
<center><iframe src="assets/calories_hist.html" width=650 height=500 frameBorder=0></iframe></center>
This distribution is evidently skewed right. We can see that there a lot more reciples
with less calories than there are with a lot. Having high amounts of calories in a recipe normally
has a negative connotation in that it is unhealthy. It makes sense for people to only post
recipes in which the calories are less so others actually want to make the dish and feel reasonably healthy
in doing so.

<br>

For the purposes of this visualization, we will treat `Average Rating` as
a categorical variable (more specifically, ordinal). We binned the ratings and
displayed the distribution of `calories` by rating, as below (scroll left and right):
<center><iframe src="assets/calories_by_rating.html" width=650 height=500 frameBorder=0></iframe></center>
<font size = '2'> <center> <em> How to read: if you hover over the blue plot (rating 1 to 2), on 250-349 calories, you will see 16.47%. This means
that for recipes that received 1 star, 16% of them had 250-349 calories. Also note that we opted against the use of a lineplot since
around 75% of the values in Average Rating were 1, 2, 3, 4, or 5. Thus, in a lineplot, most of the datapoints would be clustered around
those values and the plot would be extremely messy. </em> </center> </font>


For similar reasons as mentioned in the description of the previous graph, all distributions are skewed right.
Though the peaks of each distribution may be slightly different, there is not a visibily drastic difference
in distributions among each of the ratings. We thought that maybe higher calorie foods would be more common
in the higher rated categories because higher calories normally means more sugar, more sweetness, etc. And people
tend to like these sweeter foods. However, we were still intigued by this the relationship between sweetness and calories and
continued to test it in our analysis (as shown later in this page).

#### Aggregate Analysis
Another way that we found to quantify sweetness was to see if sugar was in the ingredients list of the recipe.
Though the `ingredients` column looks like a Python list, it is actually stored as a string, so it was pretty
simple to extract whether or not a recipe had sugar. Any type of sugar was counted like powdered sugar, brown sugar,
caned sugar, etc. In doing so, we added a new column to our dataset that simply contained boolean values for whether
or not the recipe contained sugar. We then grouped by this column and aggregated by mean. Here is the resulting dataframe:

| contains_sugar   |   minutes |   n_steps |   n_ingredients |   Average Rating |   calories |
|:-----------------|----------:|----------:|----------------:|-----------------:|-----------:|
| False            |   65.5412 |   9.54549 |         8.9805  |          4.63006 |    377.583 |
| True             |   75.5849 |  11.1645  |         9.69367 |          4.6149  |    382.39  |

<font size = '2'> <center> <em> Note that the id columns was dropped (even though it is numerical) due its mean being meaningless </em> </center> </font>

<br>
Some takeaways:
- Most of the columns have a greater mean for recipes that contain sugar (more steps, more ingredients, more minutes, on average)
- It is suprising to see that the average rating is higher for recipes without sugar (but it is fairly close)
- It does seem like there are more calories in recipes that contain sugar, but it is not evident if this is a significant difference yet

## Assessment of Missingness
#### Not Missing At Random (NMAR) Analysis
We believe that none of the columns present are NMAR. When looking at our cleaned dataset, there is only one column present with missing null values to analyze the missingness of, which is `Average Rating`. There are two ways we can establish that the data is NMAR: collecting more data or reasoning about the data generating process. For the scope of the project, we disregarded collecting more data to determine if the column is NMAR and simply focused on the data generating process for average recipe ratings. Our average rating column is derived from all the ratings for each recipe in the original dataset; this indicates that recipes with a missing average rating had no ratings in the first place. We could argue that the data is NMAR. For example, we could say that people are more likely to cook recipes that are previously rated and then rate the recipe themselves, and establish the data is NMAR. However, we believe that this is not the main reason the data is missing. We believe the missingness of the data is dependent on other confounding factors in the dataset, such as the length of time required for the recipe or the number of steps listed to make the recipe. For that reason, we believe that there are no columns with NMAR missingness; we further explored the missingness below.

#### Other Missingness Dependency Analyses
We hypothesized that the missingness in `Average Rating` was related to the `calories` column. We wanted to show that the average ratings were missing at random, or MAR, based on the calories in the recipe. Specifically, we hypothesized that recipes with higher calories would result in it being more likely that the rating was missing. We constructed `missing_rating` column from our dataset which listed `True` for the recipes that were missing their rating and `False` if it did have a rating. Below is the distribution of `calories` based on the missingness of `Average Rating`:

<center><iframe src="assets/cal_ar_missing.html" width=650 height=500 frameBorder=0></iframe></center>

We can see some slight differences in the distributions. We can see that for many bins after the peak of the graphs, the `True` distribution is greater than the `False`. This would increase the mean of the `True` distribution. To quantify the significance of any possible difference, we ran a permutation test with the following hypotheses and parameters:

- *Null Hypothesis*: The distribution of `calories` when `Average Rating` is missing is the same as the distribution of `calories` when `Average Rating`is not missing. 
- *Alternative Hypothesis*: The distribution of `calories` when `Average Rating` is missing is higher than the distribution of `calories` when `Average Rating` is not missing.
- *Test Statistic*: signed difference in means (avg calories missing avg rating - avg calories with avg rating)
- *Observed test statistic*: 37.75 calories

This is the result of our permutation test where the red line is the observed test statistic:

<center><iframe src="assets/mar.html" width=650 height=500 frameBorder=0></iframe></center>

As evident from above, our p-value under the null hypothesis was 0, meaning that it was highly unlikely that we see our observed test statistic under the null hypothesis. For this reason, we can reject the null hypothesis and conclude that the missingness of the data in `Average Rating` is has dependency on the `calories` column.

<br>

We also explored whether the missingness of `Average Rating` was related to the length of the recipe name. We already have our “missing_rating” column from the previous permutation test, but we needed to create a column with the length of each recipe name. We added a new column with the length of recipe names, `name_length` and plotted two distributions (name length with average ratings and name length with missing average ratings). There were no outliers that we needed to account for. Below is the distribution of `name_length` based on the missingness of `Average Rating`:

<center><iframe src="assets/length.html" width=650 height=500 frameBorder=0></iframe></center>

The distributions seem pretty similar. We ran a permutations will the following hypotheses / parameters:

- *Null Hypothesis*: The distribution of `name_length` when `Average Rating` is missing is the same as the distribution of `name_length` when `Average Rating` is not missing.
- *Alternative Hypothesis*: The distribution of `name_length` when `Average Rating` is missing is higher than the distribution of `name_length` when `Average Rating` is not missing.
- *Test Statistic*: signed difference in means (avg recipe time missing avg rating - avg recipe time with avg rating)
- *Observed test statistic*: 0.16 words

This is the result of the permutation test:
<center><iframe src="assets/not_mar.html" width=650 height=500 frameBorder=0></iframe></center>

We found that our p-value under the null hypothesis was 0.225, meaning that it was likely that we see our observed test statistic under the null hypothesis. For this reason, we  fail to reject the null hypothesis. We do not have enough evidence that the missingness of the data in `Average Rating` is dependent on the `name_length` column.

## Hypothesis Testing
Here we will answer the question posed at the top of the page, in relation to recipes with sugar. We hypothesized that the presence of sugar would lead to a higher overall calorie count in recipes. To answer our question, we knew we would need to conduct a permutation test because we are trying to determine if the sample with sugar present comes from the same distribution as the sample without sugar. In order to do this, we shuffled the `contains_sugar` column and computed the corresponding test statistic 1000 times. Details of `contains_sugar` can be found in the AGGREGATE ANALYSIS section.

We ran the permutation test with the following hypotheses and parameters:
- *Null Hypothesis*: There is no difference in average calories between recipes that contain sugar versus recipes that do not contain sugar.
- *Alternative Hypothesis*: The average calories between recipes that contain sugar is higher than recipes that do not contain sugar.
- *Test Statistic*: signed difference in means (avg calories with sugar - avg calories without sugar)
- *Observed Test Statistic*: 4.81 calories
- *Significance Level*: 0.05

These are the results of the permutation test:
<center><iframe src="assets/final_perm.html" width=650 height=500 frameBorder=0></iframe></center>

We found that our p-value under the null hypothesis was 0.02, meaning that it was unlikely that we would see our observed test statistic under the null hypothesis. For this reason, we could reject the null hypothesis. Because there could be other confounding factors, we cannot fully conclude that sugar is the reason why recipes have a higher calorie count; however, this test does give us some evidence toward that conclusion.
