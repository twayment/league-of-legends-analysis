# League of Legends Role Analysis

by Trey Wayment (twayment@umich.edu)

## Introduction

This project uses post-game data from professional League of Legends (LoL) matches played in 2022 to explore how player roles affect performance. The dataset comes from [Oracle’s Elixir](https://oracleselixir.com). Each match has 12 relevant rows: one for each of the 5 players on both teams and 2 containing summary data for the two teams. The dataset contains 150,588 rows and 161 columns.

My project is centered around the question:
**Which role “carries” (does the best) in their team more often: ADCs (Bot laners) or Mid laners?**

This question is interesting because it's something that League players constantly debate — do ADCs or Mid laners carry more often? By digging into real pro-level data, we can actually get some objective insight into that debate.

This dataset is valuable because it captures how different roles behave statistically in high-level play. That kind of information is helpful for coaches, analysts, and players trying to understand what performance looks like in each position.

The following columns from the dataset are most relevant to my analysis:
- `position`: The player's in-game role.
- `kills`: Number of kills the player secured.
- `deaths`: Number of times the player died.
- `assists`: Number of kills the player assisted.
- `xpat10`: The players XP (experience level) 10 minutes into the game.
- `visionscore`: A metric measuring vision control for the player player (e.g., wards placed/cleared).
- `cspm`: The players Creep Score per Minute — minions killed per minute, indicating farm efficiency.
- `totalgold`: The players total gold earned in the game — reflects resource efficiency.
- `damageshare`: Percentage of team damage dealt by the player — a measure of impact.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

To get the dataset ready for analysis, I took a few steps to clean things up and focus on the parts that actually matter for my analysis:

To get the dataset ready for analysis, I took a few steps to clean things up and focus on the parts that actually matter for my analysis:

1. **Kept only complete rows**  
   The dataset has a `datacompleteness` column that marks whether each row is fully recorded. I filtered the data to include only rows marked as `'complete'`, which helped get rid of any matches that were missing information.

2. **Cleaned up binary columns**  
   A lot of the columns that start with `'first'` — like `firstblood` and `firstdragon` — are essentially yes-or-no values, but they weren’t stored as `bool` types in the dataset. I converted them (along with the `result` column) to `True`/`False` so they’d be easier to work with if needed. I didn’t end up using these columns in the final analysis, but it was still useful to clean them for completeness.

3. **Kept only the relevant columns**  
    To simplify things further, I selected just the columns used in my analysis — the same ones I highlighted as most relevant in the introduction.

These are the first 5 rows of the cleaned dataset:

| position   |   kills |   deaths |   assists |   xpat10 |   totalgold |   visionscore |   damageshare |   cspm |
|:-----------|--------:|---------:|----------:|---------:|------------:|--------------:|--------------:|-------:|
| top        |       2 |        3 |         2 |     4909 |       10934 |            26 |     0.278784  | 8.0911 |
| jng        |       2 |        5 |         6 |     3484 |        9138 |            48 |     0.208009  | 5.1839 |
| mid        |       2 |        2 |         3 |     4556 |        9715 |            29 |     0.252086  | 6.7601 |
| bot        |       2 |        4 |         2 |     3103 |       10605 |            25 |     0.196358  | 7.9159 |
| sup        |       1 |        5 |         6 |     2161 |        6678 |            69 |     0.0647631 | 1.4711 |

### Univariate Analysis

<iframe
    src="assets/damage_share.html"
    width="800"
    height="410"
    frameborder="0"
></iframe>

This histogram displays the distribution of `Damage Share`, or the percentage of a team’s damage dealt by each player.  
The shape of the distribution suggests that damage output isn’t evenly distributed among all players, which supports the idea that certain roles — like Mid or ADC — are more likely to carry.

<iframe
    src="assets/creep_score_per_minute.html"
    width="800"
    height="410"
    frameborder="0"
></iframe>

This histogram shows that most players fall between 5–10 `Creep Score Per Minute (CSPM)`, with a right-skewed distribution. Since high farm rates are often linked to carry potential, CSPM is useful for comparing Mid laners and ADCs.

### Bivariate Analysis

<iframe
    src="assets/kill_dist_by_role.html"
    width="800"
    height="410"
    frameborder="0"
></iframe>

This box plot shows that ADCs tend to have slightly higher kill counts than Mid laners. This supports the idea that ADCs frequently take on the primary carry role through kill participation.

<iframe
    src="assets/cspm.html"
    width="800"
    height="410"
    frameborder="0"
></iframe>

This box plot shows that ADCs also tend to have higher and more consistent CSPM than Mid laners. This suggests that ADCs contribute through superior farming efficiency.

### Interesting Aggregates

The table below shows the average `damage share`, `kills`, and `Creep Score Per Minute (CSPM)` for each player role.

| position   |   damageshare |   kills |   cspm |
|:-----------|--------------:|--------:|-------:|
| bot        |          0.26 |    4.26 |   8.73 |
| jng        |          0.16 |    3.09 |   5.68 |
| mid        |          0.26 |    3.55 |   8.27 |
| sup        |          0.08 |    0.9  |   1.13 |
| top        |          0.23 |    2.8  |   7.81 |

Bot laners (ADCs) and Mid laners have the highest damage share and CSPM, which fits their role as primary damage dealers. Bot laners also have the highest average number of kills, suggesting that ADCs may carry their team more often than Mid laners.

### Imputation

I didn’t perform any imputation because all of the columns used in my analysis were complete after filtering the dataset to only include rows where `datacompleteness` == `complete`. Since none of the key columns had missing values, there was no need to fill in or estimate any data. This is ideal, as it means my analysis is based entirely on actual observed values rather than approximations.

### Framing a Prediction Problem

The goal of my prediction task is to **determine which role (top, jungle, mid, bot/ADC, or support) a player played based on their post-game data**. This is a multiclass classification task since there are five possible roles.

The response variable is `position`, and I chose it because roles are central to understanding player behavior in League of Legends. Predicting a player’s role based on their performance can reveal how distinct the roles are and whether they exhibit unique patterns.

I used the post-game features: `kills`, `deaths`, `assists`, `totalgold`, `visionscore`, `xpat10`, and `cspm`, These are all values that are available after a match finishes, so everything used is known at the time of prediction.

I evaluated the model using **accuracy**, since all five classes are relatively balanced in size. I didn’t use F1-score because the classification problem isn’t highly imbalanced — so tracking precision and recall separately wasn’t necessary.

### Baseline Model

For my baseline model, I used three quantitative parameters: `kills`, `xpat10`, and `cspm`. These features were chosen because they reflect key aspects of a player’s performance, which differ across roles. In particular, `xpat10` is a strong indicator for identifying bot lane players, since ADCs and supports share a lane and typically have lower experience compared to solo laners. All features are quantitative and were passed directly into a RandomForestClassifier inside a simple pipeline, without any scaling or feature engineering.

The model achieved an accuracy of **80%**, which I think is pretty good for a simple model. It shows that even basic stats like kills, early-game XP, and CS per minute can go a long way in predicting player roles.

### Final Model

For my final model, I used five quantitative features: `kills`, `xpat10`, `cspm`, and two that I engineered — `kda` and `vision_efficiency`. I created these using a FunctionTransformer directly inside the pipeline. KDA (kills + assists divided by deaths) reflects a player’s impact while staying alive, and vision efficiency (vision score divided by total gold) captures how effectively a player contributes vision relative to their resources — especially helpful for identifying Supports.

Since I used a RandomForestClassifier, I didn’t apply StandardScaler. Random Forests aren’t sensitive to the scale of features, so scaling would’ve added unnecessary complexity without any benefit.

For hyperparameter tuning, I used GridSearchCV with 5-fold cross-validation to test combinations of n_estimators and max_depth. The best combination turned out to be n_estimators=100 and max_depth=10, with a best cross-validation accuracy of 82.8%.

The final model achieved an accuracy of **82.5%**, improving on the baseline model’s 80%. This tells us that the model correctly predicts a player's role based off their post game data 82.5% of the time. I’m pretty happy with how it turned out — it feels like a solid model that does a good job without being overly complicated.