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
4. In order to prepare for research question, we extracted the calories from the 
`nutrition` column.
