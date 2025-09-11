# Analyzing Champion Impact in League of Legends Results
Author: Jocelyn Miao

## Overview
This data science project conducted at UCSD focuses on exploring the relationship between match results and champion picks in the game League of Legends.

## Introduction 
League of Legends (LOL) is a popular multiplayer online game developed by Riot Games that has an international playerbase of millions. LOL Esports is one of the most watched Esports today, with millions of concurrent viewers per game. The dataset used in this project is developed by Oracle’s Elixir, and records information for every match played in professional LOL Esports for 2022. It captures statistics for each match, including team strategies, player performance, and meta data. One of the most important game result influences is the 'draft' of each team, which is the combination of 5 champions within LOL that team will play in the match. Different champions have different strengths and weaknesses, and some can 'counter' (have a stronger response to) other champions. Additionally, Riot implements changes to different champions in patches, in which they can decide to make them weaker or stronger.

Thus, some champions are inherently stronger than others, and can create an advantage in deciding who wins the game. These champions are often described as 'pick-or-ban' because their strong influence on a game forced teams to do either option instead of ignoring the champion. This project seeks to answer if picking a champion that is frequently banned impacts the win rate of the team. Furthermore, the data analysis can create a model to predict the outcomes of matches using the draft of teams, which can be important in betting contexts.

The dataset `matches` has 150588 rows and 164 columns, but there are 12 rows for each match (2 for teams, 10 for players). Only the teams rows will be used, since it has relevant draft information and is the focus of this project. Important columns include: 

| Column(s) | Description |
|:---------|:--------|
| `gameid` | Unique identifier for each match |
| `patch` | Which game version the match was played on |
| `position` | What game position each player played, or if the row describes a team (used for cleaning)|
| `league` | League the match is played in |
| `side` | Which game side each team is on |
|`champion` | Which champion each player chose for the match (used for cleaning) |
| `ban1`, `ban2`, `ban3`, `ban4`, `ban5` | The team's 5 bans for the match |
| `pick1`, `pick2`, `pick3`, `pick4`, `pick5` | The team's 5 picks for the match |
| `result` | Indicates which team won the match (1 if won, 0 if lost) |

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
A filter will be applied to `position` for only rows that describe teams, since individual player performance is not considered. Only the relevant columns shown above will be in the final dataset. Additionally, some player picks are only listed under the `champions` column, but not under the team's `pick` columns, so those will need to be transfered to the team's `pick` columns using a helper function. One section of `patch` is missing likely due to input mistakes, but only for a specific tournament (LDL Playoffs) which were [all played on 12.14](https://lol.fandom.com/wiki/LDL/2022_Season/Summer_Playoffs), so that will be filled in. The columns only used for cleaning the dataframe will be dropped. 

Here is the cleaned version of the dataframe:

| gameid                |   patch | side   |   result | league   | pick1... | ban1...  |
|:----------------------|--------:|:-------|---------:|:---------|:---------|:---------|
| ESPORTSTMNT01_2690210 |   12.01 | Blue   |        0 | LCKC     | Renekton | Karma    |
| ESPORTSTMNT01_2690210 |   12.01 | Red    |        1 | LCKC     | Jinx     | Lee Sin  |
| ESPORTSTMNT01_2690219 |   12.01 | Blue   |        0 | LCKC     | Lee Sin  | Sona     |
| ESPORTSTMNT01_2690219 |   12.01 | Red    |        1 | LCKC     | Renekton | LeBlanc  |
| 8401-8401_game_1      |   12.01 | Blue   |        1 | LPL      | Jinx     | Renekton |

Not depicted are the rest of the `pick` and `ban` (2-5) columns for simplicity. Overall, we have 12549 valid matches to work with in this dataset.

### Univariate Analysis
The most picked and banned champions overall can be seen by combining all picks or bans and arranging histograms. For simplicity, these graphs will show the data over the whole year, regardless of patch changes.

<iframe
  src="assets/univarp1.html"
  width="800"
  height="400"
  scrolling="no"
  frameborder="0"
></iframe>

The top five most picked champions are Nautilus, Aphelios, Jinx, Viego, and Gnar. For 2022, these were the most chosen champions by players, and the data is not uniform across champions.

<iframe
  src="assets/univarp2.html"
  width="800"
  height="400"
  scrolling="no"
  frameborder="0"
></iframe>

The top five most banned champions are Zeri, Gwen, LeBlanc, Lucian, and Ahri. Zeri and Gwen are significantly more banned than other champions, suggesting their strength. Again, the data is not uniform across all champions.

Some characters are picked and banned much more than others, which makes sense considering game imbalances. Additionally, the top five characters banned are all 'carry' (ones that are considered damage play-makers) champions, suggesting that teams prioritize banning play-making champions. The data suggests that teams are biased towards certain champions, likely because they are stronger than their counterparts.

<iframe
  src="assets/univar.html"
  width="800"
  height="400"
  scrolling="no"
  frameborder="0"
></iframe>

Now it becomes more obvious who is most influential overall: Zeri, Gwen, Nautilus, Ahri, and Viego. This is composed of characters from both the top five banned or picked champions.

### Bivariate Analysis
To compare multiple columns, we can look at what the win rate of each champion is by looking at the `results` column. Since champions with low pick rates will have extreme win/loss rates (for example, winning the one game a champion was picked once results in 100% win rate), I will filter the data for only champions that have been picked more than or equal to 50 times.

<iframe
  src="assets/bivar.html"
  width="800"
  height="400"
  scrolling="no"
  frameborder="0"
></iframe>

From the graph, it seems that most champions are near the ideal, balanced win rate of 50% (labeled with a dotted line). However, notice that Zeri, who had the highest presence, did not have the highest win rate. Some of the popularly picked champions also had lower win rates, while unpopular champions had higher win rates. This implies that win rate may not necessarily be related pick rate. Since some champions have higher or less than 50% win rate, it can be expected that seeing a champion on a team affects their win probability for that match.

We can also look at ban rate versus win rate to see if more frequently banned champions win more. Again, I have filtered for only champions picked and banned more than 50 times.

<iframe
  src="assets/bivar2.html"
  width="800"
  height="400"
  scrolling="no"
  frameborder="0"
></iframe>

There seems to be a very small positive correlation between win rate and ban rate, but with a lot of variability, especially since champion bans are quite skewed.

### Interesting Aggregates

Using `groupby` and mode, we can see the most picked and banned champions in their respective orders for each patch. This way, changes between patches become more obvious. For example, Jinx is a staple first pick for several patches, but eventually drops off (likely due to patch changes). This indicates that different patches will have different top picked or banned champions, or that teams eventually drop priority of certain champions to lower pick orders.

|   patch | pick1   | ban1               |
|--------:|:--------|:-------------------|
|   12.01 | Jinx    | Caitlyn            |
|   12.02 | Jinx    | Caitlyn            |
|   12.03 | Jinx    | Zeri               |
|   12.04 | Jinx    | Zeri               |
|   12.05 | Jinx    | Zeri               |
|   12.06 | Jinx    | ['LeBlanc' 'Zeri'] |
|   12.07 | Sion    | Lucian             |

Similarly, we can see the most picked and banned champions in respective orders by `league`.

| league   | pick1    | pick2    | pick3           | pick4   | pick5   | ban1         | ban2    | ban3         | ban4    | ban5    |
|:---------|:---------|:---------|:----------------|:--------|:--------|:-------------|:--------|:-------------|:--------|:--------|
| LPL      | Jinx     | Aphelios | Nautilus        | Gnar    | Gnar    | Zeri         | Zeri    | Zeri         | LeBlanc | LeBlanc |
| LEC      | Jinx     | Trundle  | ['Ahri' 'Gwen'] | Gnar    | Rakan   | Twisted Fate | Yuumi   | Zeri         | Rakan   | Rakan   |
| LCK      | Aphelios | Lee Sin  | Nautilus        | Gwen    | Gnar    | Zeri         | Zeri    | Twisted Fate | Gwen    | Gwen    |
| LCS      | Zeri     | Jinx     | Lulu            | Viktor  | Gnar    | Zeri         | Kalista | Caitlyn      | LeBlanc | LeBlanc |

Or by `side`. Note that seeing Zeri in Red, ban1|ban2|ban3 makes sense. This is because Blue side always picks before Red, meaning the most important ban (as seen mostly to be Zeri) will be banned in the first ban phase (first three bans).

| side   | pick1    | pick2   | pick3    | pick4    | pick5    | ban1    | ban2   | ban3   | ban4     | ban5    |
|:-------|:---------|:--------|:---------|:---------|:---------|:--------|:-------|:-------|:---------|:--------|
| Blue   | Jinx     | Viego   | Nautilus | Jinx     | Nautilus | Kalista | Gwen   | Zeri   | Nautilus | Camille |
| Red    | Aphelios | Viego   | Nautilus | Nautilus | Nautilus | Zeri    | Zeri   | Zeri   | Nautilus | LeBlanc |


## Assessment of Missingness
### NMAR Analysis
I believe the columns `ban1` ... `ban5` are all Not Missing At Random. In LOL Esports matches, players can choose to not ban a champion or run out of time to ban one, which would result in a missing value. These columns have no trends in missingness relating to the other columns in the dataset. 

### Missingness Dependency
In the original dataset, the `pick1` (including the other `pick` columns, but looking at 1 applies to all) column was missing for several matches for team rows. I will test this missingness against the columns `league` and `result`.

First, `pick1` missingness and `league`:

**Null Hypothesis**: The distribution of `league` is the same when `pick1` is missing vs not missing.

**Alternate Hypothesis**: The distribution of `league` is different when `pick1` is missing vs not missing.

For this project, I will be using a significance level of 0.05 and I will do a permutation test using TVD.

<iframe
  src="assets/missingbar1.html"
  width="500"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>


<iframe
  src="assets/missinggraph1.html"
  width="800"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>

The observed TVD was 0.824 with a p-value of 0.0. From this result, we will reject the null hypothesis in favor of the alternate hypothesis, that the distribution of `league` is different when `pick1` is missing vs not missing. 

Next, `pick1` missingness and `result`:

**Null Hypothesis**: The distribution of `result` is the same when `pick1` is missing vs not missing.

**Alternate Hypothesis**: The distribution of `result` is different when `pick1` is missing vs not missing.

<iframe
  src="assets/missingbar2.html"
  width="500"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>


<iframe
  src="assets/missinggraph2.html"
  width="800"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>

The observed TVD was 0.0 with a p-value of 0.992. From this result, we will fail to reject the null hypothesis that the distribution of `result` is the same when `pick1` is missing vs not missing.

## Hypothesis Testing
As discussed in the introduction, I will be using a permutation test to determine if picking a champion that is frequently banned (defined as the top 5 banned champions per patch) impacts the win rate of the team. Because champion power varies from patch to patch (thus each patch has different top bans), I will take that into consideration and calculate patches separately.

**Null Hypothesis**: The win rate of teams who picked a top five banned champion is equal to the win rate of teams that did not. 

**Alternate Hypothesis**: The win rate of teams who picked a top five banned champion is greater than the win rate of teams that did not.

<iframe
  src="assets/hypoth.html"
  width="600"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>

The observed TVD was 0.0516 with a p-value of 0.0. From this result, we will reject the null hypothesis in favor of the alternate hypothesis, that the win rate of teams who picked a top five banned champion is greater than the win rate of teams that did not. This suggests that teams who draft a champion that is within the top five banned champions of that patch will have an advantage against teams that did not.

## Framing a Prediction Problem
My model will predict the outcomes of matches using the draft of teams. This will be binary classification as the response variable is whether the team wins or loses, which is the most important result of any match. I will be using accuracy as the main evaluation metric since win rate is more balanced at the professional gameplay level, since all players are at the top of their skill level. As a secondary, I will consider the F1-score for more insight on my model's predictiveness. At the time of the prediction, the only thing we will know is the patch, each team's side, and the five picks and bans of each team!

## Baseline Model
I will create a basic model using sklearn pipeline to encode and utilize a random forest classifier with training and test sets. My features will be all five of my `pick` columns, which are all nominal variables. I will use One Hot encoding to transform the columns for the model since they are nominal. 

My model has an accuracy of 51.99%, with the F1-score being very similar. This is pretty bad considering there are only two options, so this would be slightly better than just randomly guessing who won or lost.  

## Final Model
I will be adding more features to the data. Currently, my dataframe considers each match in two rows, so it doesn't account for the entire draft of a match, just one side. To fix this, I will be adjusting my dataframe to have columns for all five `pick`s and `enemy_pick`s in each row. This way, one match is one row, and we can account for opposing champion matchups. Additionally, we can add a feature from my hypothesis test, `picked_top_ban`, for both sides, as well as the side itself. My test indicated that picking a top-banned champion can benefit the win rate of a team. Now the features being used are `pick`s, `enemy_pick`s (five each), `side`, `picked_top_ban`, and `picked_top_ban_enemy`.

To improve upon the `RandomForestClassifier` in the baseline model, I will be using `GridSearchCV` to tune the hyperparameters `max_depth`, `n_estimators`, `min_samples_leaf`, and `min_samples_split` to reduce variance. 

Now the accuracy is now 53.67%, which is 1.68% more than the baseline model. Although not my main evaluation metric, I noticed that the F1-score improved by 14.25%, which indicates improvement in predictiveness.

## Fairness Analysis
I will be doing the fairness analysis with two groups: teams in Tier 1 leagues (LCS, LCK, LPL, LCP, and LEC) and teams not in Tier 1 leagues. Major tournaments will be excluded since they can contain a mix of Tier 1 and non-Tier 1 teams. I chose these categories because the level of play is regarded as much higher between the tiers and they are allocated spots at international tournaments. My evaluation metric will be F1-score since the two groups are imbalanced (there are much more below Tier 1). 

**Null Hypothesis**: The model is fair. The F1-scores for Tier 1 and non-Teir1 teams are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis**: The model is unfair. The F1-scores for Tier 1 and non-Teir1 teams are not the same.

<iframe
  src="assets/fairness.html"
  width="600"
  height="500"
  scrolling="no"
  frameborder="0"
></iframe>

I got a p-value of 0.68, which is larger than my significance level of 0.05, so I will fail to reject the null hypothesis. This implies that the model predicts results for both Tier 1 and non-Tier 1 leagues similarly, making it fair.