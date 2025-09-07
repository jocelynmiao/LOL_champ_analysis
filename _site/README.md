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
A filter will be applied to `position` for only rows that describe teams, since individual player performance is not considered. Only the relevant columns shown above will be in the final dataset. Additionally, some player picks are only listed under the `champions` column, but not under the team's `pick` columns, so those will need to be transfered to the team's `pick` columns. One section of `patch` is missing likely due to input mistakes, but only for a specific tournament (LDL Playoffs) which were [all played on 12.14](https://lol.fandom.com/wiki/LDL/2022_Season/Summer_Playoffs), so that will be filled in. The columns only used for cleaning the dataframe will be dropped. 

Here is the cleaned version of the dataframe:

| gameid                |   patch | side   |   result | league   | pick1    | pick2     | pick3    | pick4   | pick5     | ban1     | ban2         | ban3         | ban4     | ban5    |
|:----------------------|--------:|:-------|---------:|:---------|:---------|:----------|:---------|:--------|:----------|:---------|:-------------|:-------------|:---------|:--------|
| ESPORTSTMNT01_2690210 |   12.01 | Blue   |        0 | LCKC     | Renekton | Samira    | Xin Zhao | LeBlanc | Leona     | Karma    | Caitlyn      | Syndra       | Thresh   | Lulu    |
| ESPORTSTMNT01_2690210 |   12.01 | Red    |        1 | LCKC     | Jinx     | Viego     | Gragas   | Viktor  | Alistar   | Lee Sin  | Twisted Fate | Zoe          | Nautilus | Rell    |
| ESPORTSTMNT01_2690219 |   12.01 | Blue   |        0 | LCKC     | Lee Sin  | Jhin      | Gragas   | Rakan   | Orianna   | Sona     | Jarvan IV    | Caitlyn      | Lulu     | Lucian  |
| ESPORTSTMNT01_2690219 |   12.01 | Red    |        1 | LCKC     | Renekton | Syndra    | Nidalee  | Leona   | Gangplank | LeBlanc  | Yuumi        | Twisted Fate | Karma    | Alistar |
| 8401-8401_game_1      |   12.01 | Blue   |        1 | LPL      | Jinx     | Jarvan IV | Nautilus | Syndra  | Gwen      | Renekton | Lee Sin      | Caitlyn      | Jayce    | Camille |

### Univariate Analysis
The most picked and banned champions overall can be seen by combining all picks or bans and arranging histograms. For simplicity, these graphs will show the data over the whole year, regardless of patch changes.

<div class="iframe-container">
  <iframe src="assets/univar.html" scrolling="no"></iframe>
</div>

<iframe
  src="assets/univar.html"
  width="1000"
  height="540"
  scrolling="no"
  frameborder="0"
></iframe>

