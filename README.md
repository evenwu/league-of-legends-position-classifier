# "How Player Stats Reveal Their Position" - Analysis on 2025 League of Legends esports match data
by Even Wu (wew062@ucsd.edu)

---

## Introduction

League of Legends (LoL) is a team‑based competitive strategy game where two teams of five players battle on a map called Summoner’s Rift. Each player controls a unique champion with distinct abilities. Since its release in 2009, League of Legends has grown into one of the world’s most influential esports, drawing millions of players and viewers worldwide. 

To a new audience, competitive League of Legends may look chaotic on the surface, ten players skirmishing across the map, trading kills, securing objectives. But beneath that chaos lies a consistent structure. Every player occupies one of five positions, each with specific responsibilities and playstyle:[^1]

- **Top Laner**: Defend lane, apply pressure, usually playing alone

- **Jungler**: Control the jungle, assist other lanes, secure neutral objectives

- **Mid Laner**: Control the center of the map, engage in quick fights, roam to assist other lanes

- **Bot Laner**: aka the ADC (Attack Damage Carry), deals sustained damage, build level advantage

- **Support**: Protect and assist ADC, provide vision and utility

[^1]: https://www.redbull.com/int-en/league-of-legends-roles-explained
<img width="700" height="498" alt="image" src="https://github.com/user-attachments/assets/95610568-3a0c-42ed-9f77-f498af12e74f" />

This project will investigate the question:

## **Is there a difference in the distribution of post-game statistics between positions, and which statistics most clearly distinguish each role?**

If post‑game stats do differ meaningfully by position, then we can identify which metrics are most distinctive for each role — helping players understand what truly defines their position and make more informed decisions about how to improve.

We have access to a dataset from [Oracle's Elixir](https://oracleselixir.com/tools/downloads) containing information about over 10,000 League of Legends esports matches from 2025. The dataset contains 120636 rows, and the relevant columns are:

- `position` — The in‑game role the player occupied (top, jng, mid, bot, sup).

- `gamelength` — Total duration of the match, measured in seconds.

- `kills` — Number of enemy champions the player eliminated.

- `deaths` — Number of times the player was killed by the enemy team.

- `assists` — Number of kills the player contributed to without landing the final blow.

- `earnedgoldshare` — The percentage of the team’s total earned gold that this player received.

- `damageshare` — The percentage of the team’s total damage dealt by this player.

- `damagetakenperminute` — Average amount of damage the player absorbed per minute.

- `vspm` (vision score per minute) — Vision Score measures how much a player contributes to their team’s vision control throughout a match.

---

## Data Cleaning and Exploratory Data Analysis

### Data cleaning

Every 12 rows in the dataset corresponds to one match, with 10 rows representing the players and 2 rows for each team. Since our analysis is only focused on player-level statistics, we first remove all team-level rows. Then we extract only the relevant columns to create the lol_cleaned dataframe.

Below are the first 5 rows of `lol_cleaned`:

|    | position   |   gamelength |   kills |   deaths |   assists |   earnedgoldshare |   damageshare |   damagetakenperminute |   vspm |
|---:|:-----------|-------------:|--------:|---------:|----------:|------------------:|--------------:|-----------------------:|-------:|
|  0 | top        |         1592 |       1 |        2 |         1 |         0.289981  |     0.40197   |                681.219 | 0.6407 |
|  1 | jng        |         1592 |       0 |        3 |         1 |         0.15852   |     0.0989769 |                906.972 | 1.093  |
|  2 | mid        |         1592 |       1 |        2 |         0 |         0.223581  |     0.278244  |                366.859 | 0.7538 |
|  3 | bot        |         1592 |       1 |        3 |         1 |         0.238801  |     0.137567  |                479.209 | 0.3769 |
|  4 | sup        |         1592 |       0 |        3 |         2 |         0.0891171 |     0.0832419 |                474.535 | 3.0905 |

The cleaned dataframe now has 100530 rows × 9 columns

### Univariate Analysis

Let's look at some distributions of relevant columns:

<iframe src="assets/kills_hist.html" width=630 height=420 frameBorder=0></iframe> 
<iframe src="assets/deaths_hist.html" width=630 height=420 frameBorder=0></iframe> 
<iframe src="assets/assists_hist.html" width=630 height=420 frameBorder=0></iframe>

It looks like the distributions of the `kills`, `deaths`, and `assists` columns (K/D/A) are all right-skewed, with the bulk of the data clustered on the lower end of the axis. Notice that the average kill count and death count are about the same; this makes sense because for every kill, there has to be a death. Meanwhile, the average assist count is significantly higher because every kill can generate up to four assists if all five team members contributed to eliminating the target.

### Bivariate Analysis:

With the knowledge of how a player's position can affect their stats like K/D/A, we need to explore the relationship between the `position` column and the K/D/A columns: 

<iframe src="assets/KDA_bar.html" width=630 height=420 frameBorder=0></iframe>

This bar graph clearly shows how K/D/A differ between positions. Bot laners generally get more kills, and supports get significantly more assists and fewer kills.

K/D/A is not the only thing we should consider for a player's post-game stats. There are a lot of aspects of the game that K/D/A cannot fully capture. Here are two more major stats: `earnedgoldshare` and `damageshare`. Both are measured as a proportion (0~1) within the player's own team.

<iframe src="assets/gold_hist.html" width=630 height=420 frameBorder=0></iframe>
<iframe src="assets/damage_hist.html" width=630 height=420 frameBorder=0></iframe>

Both of these statistics seem to have bimodal distributions, as a result of the playstyle of support players being provided value through utility, vision, and crowd control, not through gaining gold and scaling damage.

<iframe src="assets/gold_damage_scatter.html" width=630 height=420 frameBorder=0></iframe>

There is also a positive linear relationship between 'earnedgoldshare' and 'damageshare', which is expected because League of Legends’ entire economy and combat system is built so that gold enables damage, and damage earns more gold.

### Interesting Aggregates:

We can organize all of our columns of interest and create a grouped table. Examine their aggregate **means** for every position

| position   |   gamelength |   kills |   deaths |   assists |   earnedgoldshare |   damageshare |   damagetakenperminute |   vspm |
|:-----------|-------------:|--------:|---------:|----------:|------------------:|--------------:|-----------------------:|-------:|
| top        |      1921.56 |    3.12 |     3.26 |      5.63 |              0.21 |          0.23 |                 913.15 |   0.89 |
| jng        |      1921.56 |    3.42 |     3.39 |      8.10 |              0.20 |          0.17 |                1198.86 |   1.36 |
| mid        |      1921.56 |    3.94 |     3.00 |      6.73 |              0.23 |          0.26 |                 637.16 |   0.98 |
| bot        |      1921.56 |    4.93 |     2.77 |      6.10 |              0.26 |          0.27 |                 519.92 |   1.00 |
| sup        |      1921.56 |    0.88 |     3.92 |     11.03 |              0.10 |          0.08 |                 566.02 |   3.39 |

The table summarizes average performance metrics by position. Instead of looking at thousands of individual rows, this grouped view highlights the general patterns that distinguish each role. This table also highlights that `earnedgoldshare` and `damageshare` are also correlated within each position.

---

## Assessment of Missingness

### MNAR Analysis

None of our columns of interest contains missing values, but in the original dataset, the `playerid` column has 1586 missing values, which we believe are most likely MNAR (Missing Not At Random). In professional esports, well‑established players almost always have registered IDs, so a missing player ID probably corresponds to a less‑documented player, such as a substitute. The probability of being missing depends on the identity of the player. To potentially reclassify this as MAR(Missing At Random), we would want additional metadata such as a “is_substitute” column.

### Missingness Dependency

Looking at other columns with missing values, an interesting phenomenon is that sometimes a team will skip one of their five bans. Resulting in missing values in that column.

In League of Legends, a **ban** is when a team removes a champion from the pool during the draft phase so that neither team can pick it for that game. Bans are used to limit the opponent’s strategy, so missing a ban can put a team at a competitive disadvantage.

The column `ban5` (the fifth ban) has 610 missing values. We analyze the dependency of the missingness of this column on other columns.

First, we test to see if `ban5` is MAR depending on `victory` (True / False) with hypotheses:

- H0: The distribution of `victory` when `ban5` is missing is the same as the distribution of 'victory' when 'ban5' is not missing

- H1: The distribution of `victory` when `ban5` is missing is different from the distribution of 'victory' when 'ban5' is not missing

We use TVD (Total Variation Distance) as our test statistic

Below is the observed distribution of `victory` when `ban5` is missing and not missing.

| victory   |   ban5_missing = False |   ban5_missing = True |
|:----------|-----------------------:|----------------------:|
| False     |               0.499299 |              0.614754 |
| True      |               0.500701 |              0.385246 |

After performing a permutation test, the observed TVD (0.12) has a p-value of 0. Below shows the empirical distribution of the TVD for this test.

<iframe src="assets/tvd1.html" width=630 height=420 frameBorder=0></iframe>

Since the p-value is less than the 0.05 significance level, we reject the null hypothesis, and there is strong evidence that missing ban5 is related to whether a team wins or loses. Therefore, the missingness in the `ban5` column does depend on `victory`.

Now we test to see if `ban5` is MAR depending on `side` (Red / Blue) with hypotheses:

- H0: The distribution of 'side' when 'ban5' is missing is the same as the distribution of `side` when `ban5` is not missing

- H1: The distribution of 'side' when 'ban5' is missing is different from the distribution of `side` when `ban5` is not missing

Again, we use TVD as our test statistic

Below is the observed distribution of `side` when `ban5` is missing and not missing.

| side   |   ban5_missing = False |   ban5_missing = True |
|:-------|-----------------------:|----------------------:|
| Blue   |                0.50005 |              0.491803 |
| Red    |                0.49995 |              0.508197 |

After performing a permutation test, the observed TVD (0.01) has a p-value of 0.685. Below shows the empirical distribution of the TVD for this test.

<iframe src="assets/tvd2.html" width=630 height=420 frameBorder=0></iframe>

Since the p-value is greater than the 0.05 significance level, we fail to reject the null hypothesis, and this test does not provide evidence that missing ban5 is related to whether a team is on the red side or blue side. Therefore, the missingness in the `ban5` column does depend on `side`.

---

## Hypothesis Testing

As we saw earlier, the distribution of KDA differs between positions. Bot laners have higher-than-average kill counts, and supports have higher-than-average assist counts. However, the death count averages for each position are comparatively more uniform.

<iframe src="assets/KDA_bar.html" width=630 height=420 frameBorder=0></iframe>

Is there a significant difference in the death count distribution between positions? Since support players seem to have the highest average death count. We test these hypotheses with a 0.05 significance level:

- H0: The population mean death count for supports is equal to the population mean death count for non-supports

- H1: The population mean death count for supports is greater than the population mean death count for non-supports

Test statistic: the difference in mean death count (support - non-support)

After performing a permutation test, the observed test statistic (0.82) has a p-value of 0. Below shows the empirical distribution of the difference in mean death counts

<iframe src="assets/meandiff.html" width=630 height=420 frameBorder=0></iframe>

Since the p-value is less than the 0.05 significance level, we reject the null hypothesis, and there is strong evidence suggesting that supports have a higher average death count than non-supports. This result directly advances the goal of answering our overarching question, as it identifies death count as a statistic that differs between positions

---

## Framing a Prediction Problem

To further answer our question and find the statistics that most clearly distinguish each role, we will build a classifier model to **predict which role a player played given their post-game data**.

- **Type**: multiclass classification

- **Response variable**: 'position' (top, jng, mid, bot, sup)

- **Time of prediction**: post-game (all data avaliable)

- **Evaluation metric**: macro F1 score average (forces the model to be good across all five roles, not just the easy ones)

## Baseline Model

We begin by splitting the dataset into training and testing sets using a 75/25 split to ensure that model evaluation is performed on unseen data.

For the baseline model, we use three quantitative features: `kills`, `deaths`, and `assists`. These are the most fundamental post-game statistics and provide a simple starting point for prediction. Before fitting the model, we standardize these features using `StandardScaler()`.

We choose multinomial logistic regression as our classifier because it is highly interpretable: the coefficients directly indicate how each statistic influences the probability of a player belonging to each role. This also aligns with this project's goal of understanding which statistics can distinguish positions.

After training the model on the training set and evaluating it on the test set, we obtain the following classification report:
```
              precision    recall  f1-score   support

         bot       0.38      0.45      0.41      5027
         jng       0.32      0.29      0.31      5026
         mid       0.27      0.07      0.11      5027
         sup       0.60      0.80      0.68      5026
         top       0.29      0.38      0.33      5027

    accuracy                           0.40     25133
   macro avg       0.37      0.40      0.37     25133
weighted avg       0.37      0.40      0.37     25133
```
With a macro‑averaged F1 score of 0.37, the baseline model leaves substantial room for improvement. Using only K/D/A allows the classifier to identify supports reasonably well, likely because supports have a distinct death and assist ratio, but these three features alone are not enough to reliably distinguish the other four roles.

---

## Final Model

To improve the model, first try adding more features. `damageshare`, `damagetakenperminute`, and `vspm` are good features to add because they reflect the intentional structure of the game. Each role in League of Legends has a unique combination of these responsibilities, and these features can create more separable clusters in feature space than K/D/A alone.

- **Damage Share**: who outputs damage

- **Damage Taken per minute**: who absorbs pressure

- **Vision Score per minute**: who controls infromation

We intentionally exclude `goldshare` from the model because it is highly correlated with `damageshare`. We only keep one of these features to avoid multicollinearity occurring and preserve the interpretability of the coefficients.

Since all of our added features are quantitative, we continue to use `StandardScaler()` to transform the data.

After preprocessing, we tune the logistic regression model by performing a grid search over the hyperparameter `C` using `GridSearchCV()`. This allows us to identify the level of regularization that maximizes macro‑averaged F1 performance on cross‑validation. The search selects `C=100` as the optimal value.

Below are the classification report and confusion matrix for the final model:
```
              precision    recall  f1-score   support

         bot       0.62      0.71      0.66      5027
         jng       0.85      0.85      0.85      5026
         mid       0.45      0.39      0.42      5027
         sup       1.00      1.00      1.00      5026
         top       0.65      0.63      0.64      5027

    accuracy                           0.72     25133
   macro avg       0.71      0.72      0.71     25133
weighted avg       0.71      0.72      0.71     25133
```
<iframe src="assets/cm.html" width=630 height=420 frameBorder=0></iframe>

The final model achieves an overall accuracy of 0.72 and a macro‑averaged F1 score of 0.71, a significant improvement from the baseline model.

We can see that support and junglers are distinguishable, with supports classified perfectly on our testing set. Bot and top laners can be identified fairly well, while mid laners remain the hardest role to predict due to overlapping stat profiles. 

Below is the coefficient table of the final model:

|     |   kills |   deaths |   assists |   damageshare |   damagetakenperminute |   vspm |
|:----|--------:|---------:|----------:|--------------:|-----------------------:|-------:|
| bot |    0.51 |     0.40 |     -0.19 |          1.37 |                  -2.66 |  -2.61 |
| jng |    0.18 |    -0.64 |      0.01 |         -1.06 |                   2.74 |   1.58 |
| mid |    0.02 |    -0.08 |      0.22 |          1.30 |                  -0.69 |  -2.66 |
| sup |   -0.49 |     1.01 |      0.08 |         -2.38 |                  -0.75 |   6.93 |
| top |   -0.21 |    -0.70 |     -0.12 |          0.77 |                   1.36 |  -3.24 |

Because all features were standardized, the coefficients are directly comparable. A large positive coefficient means the model strongly associates that feature with the role; a negative coefficient means the opposite. Some interpretations we can have are:

- A large positive coefficient on vision score for SUP means the model strongly associates a high vision score with support play.

- A negative coefficient on damage taken for BOT reflects that they avoid damage relative to others.

- Many K/D/A coefficients are relatively close to 0, reflecting their limited ability to distinguish positions.

Overall, the coefficient patterns show that vision score is the single most distinguishing statistic across roles, with damage share and damage taken also contributing strongly.

---

## Fairness Analysis

The duration of the esports matches in our dataset ranges from 18 minutes to 60 minutes. With the average `gamelength` being 32 minutes. We perform a fairness analysis and test whether the model performs equally well on short (below‑average length) and long (above‑average length) matches.

- H0: Our model is fair. Its accuracy for longer matches and shorter matches is roughly the same, and any differences are due to random chance

- H1: Our model is unfair. Its accuracy for shorter matches is higher than its precision for longer matches.

test stat: difference in accuracy (short - long)

<iframe src="assets/accdiff.html" width=630 height=420 frameBorder=0></iframe>

The permutation test produced a p‑value of 0; none of the shuffled datasets showed a difference as large as the one observed. We reject the null hypothesis. There is evidence that the model is less accurate on long matches and appears to be unfair.

Longer matches may be harder for the model because player statistics become more homogenized as games drag on. In extended games, every role tends to accumulate more vision, more damage, and more damage taken simply due to time, which reduces the contrast between positions.
