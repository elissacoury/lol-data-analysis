# Predicting League of Legends Role based on End-Game Stats

by Elissa Coury

---

## Introduction

This is a Data Analysis project on a 2022 League of Legends Dataset containing comprehensive information on professional games. This project was conducted for the course DSC80 at UC San Diego.
This dataset has 150588 rows (there should be 12 rows for every game, 5 for each team's players, and 1 for each team aggregated stats.)

League of Legends is a team-based battle game, each team consisting of 5 players. As games are very action packed, they provide a lot of data, inlcuding reports on collected gold/xp, kills/deaths, damage dealt/took, and other specific factors of the game like vision control/score  which has to do with information gained on the map. This allows a vast amount of questions to be asked about this dataset. I've chosen to focus on "how do contributions to game stats change based on position?" 
Relevant columns for this are:
'gameid': str, unique game id

'datacompleteness': str, 'complete' or 'partial'

'url': mostly for partial completed data rows, includes a url to game stats

'position': str, player position/role, out of 5 options (listed above) -- 'team' rows removed

'gamelength': int, length of game in seconds

'kills': int, # of kills

'deaths': int, # of deaths

'assists': int, # of assists

'firstbloodkill': float, 0=player did not get first kill, 1=player got first kill --> changed to bool type, True for got first kill, False for did not

'barons': float, # of barons

'damagetochampions': float, damage to enemy champions

'dpm': float, damage to enemy champions per minute

'damageshare': float, proportion of team damage for player (0-1)

'damagetakenperminute': float, damage taken by player per minute

'wardsplaced': float, # of wards placed (total)

'wpm': float, wards placed per minute

'wardskilled': float, total # of wards killed

'wcpm': float, wards cleared per minute

'controlwardsbought': float, # of control wards bought total

'visionscore': float, total vision score

'vspm': float, vision score per minute

'totalgold': int, total gold from game

'earnedgold': float, gold from game (excluding initial gold and inherent gold accumulation)

'earned gpm': float, earned gold per minute

'earnedgoldshare': float, proportion of team earned gold for player (0-1)

'goldspent': float, gold spent by player

'total cs': float, creep score

'minionkills': float, # of minion kills

'monsterkills': float, # of monster kills

'cspm': float, creep score per minute

'kpm': float, kills per minute

'gspm': float, gold spent per minute

Not all of these columns will be used in the final analysis for this project. 

For this question and project, I will especially be focusing on the 'position' column, which includes 6 unique values, with different important related stats:
'top': top lane; xp, gold, block/soak damage, dealing damage
'jng': jungle; xp, gold, dealing damage (requires kills), taking damage, vision control (wards)
'mid': mid lane: xp, gold, damage (should have less deaths)
'bot': bot lane (ADC); gold!, low deaths, high damage (DPS: damage per second), kill towers/barons
'sup': support; block damage, vision control (buy control wards), might have similar stats as jungler if paired up
'team': this is the aggregated stats for the team
Credits for information on these positions and their relevat stats( https://gamerant.com/league-legends-roles-explained/).

---

## Data Cleaning and Exploratory Data Analysis

As my question was specific to individual player roles, I started off with cleaning the dataset to only include rows where the 'position' value was not 'team'. This data was irrelevant to my question. This reduced the rows to 125490.

I then had to determine if there was any specific games that were missing a lot of relevant data. This is when 'datacompleteness' was important. I was able to determine that two games had missing data for relevant columns, so I decided to remove them to prevent any problems that could occur because of this later on.

I also changed the 'firstbloodkill' column to boolean type values, as they previosuly were represented as floats before and included many nan values. I also created kills per minute (kpm) and gold spent per minute (gspm) columns using the 'kills' and 'goldspent' column divided by the 'gamelength' column (made to minutes by diving by 60), to get the average. The average for these would be important because game length can vary and these values can hold different weights based on the time they were done in.



### Univariate Analysis
<iframe src="/lol-data-analysis/assets/earned-gpm-histogram.html" width="800" height="600" frameborder="0" ></iframe>

This histogram shows a distribution of the earned gold per minute among all player entries. It shows a slightly right skewed distribution. Most player's game stats for this are on the lower end, and fewer players gain high amounts of gold per minute in games.

### Bivariate Analysis
<iframe src="/lol-data-analysis/assets/avg-egpm-by-position.html" width="800" height="600" frameborder="0" ></iframe>

This shows the distribution of average earned gold per minute distinguished by posiiton (using a grouped histogram of 'earned gpm' by 'position'). This shows which positions, on average, earn less gold per minute (jungle and support).

### Interesting Aggregates

| position   |   assists |   damagetakenperminute |   deaths |     dpm |   earned gpm |    gspm |   kills |   visionscore |
|:-----------|----------:|-----------------------:|---------:|--------:|-------------:|--------:|--------:|--------------:|
| bot        |   5.37467 |                422.578 |  2.55117 | 560.926 |      299.916 | 393.866 | 4.26082 |       37.8448 |
| jng        |   6.85714 |                855.498 |  3.11465 | 325.092 |      212.393 | 317.965 | 3.05726 |       43.1138 |
| mid        |   5.89495 |                524.064 |  2.65805 | 546.337 |      267.468 | 370.39  | 3.50434 |       33.7615 |
| sup        |   9.19001 |                400.317 |  3.24536 | 172.363 |      110.589 | 220.873 | 0.87491 |       79.2151 |
| top        |   5.02403 |                734.169 |  2.95377 | 488.639 |      257.898 | 364.067 | 2.79676 |       30.8533 |

This is a pivot table, showing the averaged values for relevant columns when assessing player stats. It shows interesting information, where the bottom and support positions take less damage than other positions and jungle takes the most. Support also has much higher vision score with much loser # of average kills. These can help when formualting a prediction problem, and determing what columns provide the most information about positions.

---

## Assessment of Missingness

The columns 'goldat_', 'xpat_', 'csat_', 'opp_goldat_', 'opp_xpat_', 'opp_csat_', 'golddiffat_', 'xpdiffat_', 'csdiffat_', 'killsat_', 'assistsat_', 'deathsat_', 'opp_killsat_', 'opp_assistsat_', 'opp_deathsat_' (for 10, 15, 20, 25 minutes) would logically make sense to be NMAR. This is because their missingness is related to the data reported. If there is no data recorded for these values, it could be attributed to the fact that the game might not be occurring at that time. In this case, it's related to the nature of the feature, as it is specifically about game stats at a specific time. This makes it seem NMAR.

However, there is a gamelength column, which is in seconds. This technically makes it Missing At Random (MAR), because we can look at that column to determine if the other columns will be missing. This dependancy makes the previous columns MAR.



---

## Hypothesis Testing



---

## Framing a Prediction Problem

Prediction Problem: Predict which role (top-lane, jungle, mid-lane, bot lane, or support) a player played given their post game data.

This is a multi-class classification problem. It will use features like kpm (kills per minute), dpm (damage per minute), damagetakenperminute, and visionscore. These will be used to predict the 'position'.

---

## Baseline Model



---

## Final Model



---

## Fairness Analysis


