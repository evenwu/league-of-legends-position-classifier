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

## **Is there a difference in the distribution of post-game statistics between different positions?**

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

| position   |   gamelength |   kills |   deaths |   assists |   earnedgoldshare |   damageshare |   damagetakenperminute |   vspm |
|:-----------|-------------:|--------:|---------:|----------:|------------------:|--------------:|-----------------------:|-------:|
| top        |         1592 |       1 |        2 |         1 |         0.289981  |     0.40197   |                681.219 | 0.6407 |
| jng        |         1592 |       0 |        3 |         1 |         0.15852   |     0.0989769 |                906.972 | 1.093  |
| mid        |         1592 |       1 |        2 |         0 |         0.223581  |     0.278244  |                366.859 | 0.7538 |
| bot        |         1592 |       1 |        3 |         1 |         0.238801  |     0.137567  |                479.209 | 0.3769 |
| sup        |         1592 |       0 |        3 |         2 |         0.0891171 |     0.0832419 |                474.535 | 3.0905 |

The cleaned dataframe now has 100530 rows × 9 columns

### Univariate Analysis

Let's look at some distributions of relevant columns:
<iframe src="assets/kills_hist.html" width=600 height=450 frameBorder=0></iframe> <iframe src="assets/deaths_hist.html" width=600 height=450 frameBorder=0></iframe> <iframe src="assets/assists_hist.html" width=600 height=450 frameBorder=0></iframe>








<iframe src="assets/KDA_bar.html" width=800 height=600 frameBorder=0></iframe>

















---

## Assessment of Missingness

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```

| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 |

---

## Hypothesis Testing


---
