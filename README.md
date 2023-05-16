# Recipe Analysis
Analyzing and exploring a dataset containing information about different recipes

## Intoduction and Question Identification
We originally started with two datasets. The first was a dataset containing information 
about many recipes. For example, some columns were the name of the recipe, when it was
posted, its nutrition facts, the steps of the recipe, ingredients, etc. The second
dataset contains a recipe id that matches to the first dataset, along with reviews
for that particular recipe and the rating the person gave it among other columns.
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

A question that we explored throughout this project was *"Do recipes with sugar as
an ingredient typically have more calories than those without?"*

---

## Cleaning and EDA (Exploratory Data Analysis)
As mentioned before, we started by merging the two datasets. These are the next steps we followed:
1. We filled all the ratings that had a value of 0 with `np.nan`. We did this because the rating
scale was from 1-5, so a rating of `0` is not possible. Thus, we opted that since 0 can still have
meaning in terms of a rating (aside from the scale) we should replace it.
2. Since we saw that recipes had ratings/reviews from different users, we decided to
compute the average rating per recipe. We then added this information to our dataframe so
that each recipe had an average rating. We also dropped all duplicate recipes in our dataset
since the only differing information were the reviews given by each user and we would not be 
using that info.
3. We then dropped all unnecessary columns. These were `['contributor_id', 'submitted', 'tags', 'steps', 'description', 'user_id', 'recipe_id', 'date', 'review', 'rating_x']`. We were able to look at these columns and figure out that they would not have
any significance in our future analyses.
4. In order to prepare for research question, we extracted the calorie value for each recipe from the 
`nutrition` column.

Here is the first 5 rows of our resulting dataframe:

| name                                  |     id |   minutes |   n_steps | ingredients                                                                                                                            |   n_ingredients |   Average Rating |   calories |
|:--------------------------------------|-------:|----------:|----------:|:---------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|-----------:|
| impossible macaroni and cheese pie    | 275022 |        50 |        11 | ['cheddar cheese',...,'red pepper sauce']                                                 |               7 |                3 |      386.1 |
| impossible rhubarb pie                | 275024 |        55 |         6 | ['cream cheese',...,'milk']                                                          |               8 |                3 |      377.1 |
| impossible seafood pie                | 275026 |        45 |         7 | ['frozen crabmeat',...,'nutmeg']                     |               9 |                3 |      326.6 |
| paula deen s caramel apple cheesecake | 275030 |        45 |        11 | ['apple pie filling',...,'pecans'] |               9 |                5 |      577.7 |
| midori poached pears                  | 275032 |        25 |         8 | ['midori melon liqueur',...,'mint']        |               9 |                5 |      386.9 |

This is the distribution of the `average rating` column:
<center><iframe src="assets/average_rating_hist.html" width=600 height=400 frameBorder=0></iframe></center>
<center> It is evident that the distribution is heavily skewed left, meaning that more ratings
tend to be higher than lower. Either most of the recipes were really good or the people
who rated them were feeling nice. </center>
