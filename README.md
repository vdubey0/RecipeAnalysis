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
using that info. This left us with 83782 rows.
3. We then dropped all unnecessary columns. These were `['contributor_id', 'submitted', 'tags', 'steps', 'description', 'user_id', 'recipe_id', 'date', 'review', 'rating_x', 'nutrition']`. We were able to look at these columns and figure out that they would not have
any significance in our future analyses.
4. In order to prepare for research question, we extracted the calorie value for each recipe from the 
`nutrition` column.
5. We then noticed that there were pretty large outliers in the `minutes` and `calorie` columns.
Some recipes had calorie counts that were in the 20,000's and some recipes were taking almost 300
days. We decided to filter out these recipes because a recipe having that many calories and
taking that long is unfeasible and is probably due to input errors in the data gathering
process. We did not want these values to skew our analysis because we will be working with
means in our statistical analysis and we know that outliers can heavily skew such a measure. We
figured to set a calorie limit of 2350 (the mean of the 2200 and 2500 calories recommended for
women and men respectively). We reasoned the daily calorie limit because we thought a day to consist
of 3 meals and a couple snacks so ~4 meals. A recipe that makes more than 4 meals at once was not as
interesting to us because we wanted to work with recipes that we could encounter in our actual lives.
As for minutes, we set a minute limit of 2880 which is 1.5 days. After this cleaning process, we were left with 82472 
(around 98.5% of the data from step 2).

Here is the first 5 rows of our resulting dataframe (note that for visual purposes, the ingredients column is shortened with ellipses):

| name                                  |     id |   minutes |   n_steps | ingredients                                                                                                                            |   n_ingredients |   Average Rating |   calories |
|:--------------------------------------|-------:|----------:|----------:|:---------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|-----------:|
| impossible macaroni and cheese pie    | 275022 |        50 |        11 | ['cheddar cheese',...,'red pepper sauce']                                                 |               7 |                3 |      386.1 |
| impossible rhubarb pie                | 275024 |        55 |         6 | ['cream cheese',...,'milk']                                                          |               8 |                3 |      377.1 |
| impossible seafood pie                | 275026 |        45 |         7 | ['frozen crabmeat',...,'nutmeg']                     |               9 |                3 |      326.6 |
| paula deen s caramel apple cheesecake | 275030 |        45 |        11 | ['apple pie filling',...,'pecans'] |               9 |                5 |      577.7 |
| midori poached pears                  | 275032 |        25 |         8 | ['midori melon liqueur',...,'mint']        |               9 |                5 |      386.9 |

This is the distribution of the `calorie` column:
<center><iframe src="assets/calories_hist.html" width=650 height=500 frameBorder=0></iframe></center>
<center> <em> This distribution is evidently skewed right. We can see that there a lot more reciples
with less calories than there are with a lot. Having high amounts of calories in a recipe normally
has a negative connotation in that it is unhealthy. It makes sense for people to only post
recipes in which the calories are less so others actually want to make the dish and feel reasonably healthy
in doing so. </em> </center>




