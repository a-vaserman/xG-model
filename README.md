# NHL First-Period xG vs. Shot Volume: Predicting Game Outcomes
Does shot quality beat shot quantity at predicting NHL winners and how early can we tell?

This project builds a custom expected goals (xG) model from shot-level data and compares its predictive power against raw shot volume using only **first-period 5v5 data** across 3,936 regular season games (2022-2024).

## Findings
| Model | AUC |
|---|---|
| Shot Ratio (Corsi) | 0.585 |
| xG Ratio | 0.609 |
| Combined | 0.608 |

**xG outperforms shot volume by 2.4 AUC points.** More notably, combining both metrics adds no predictive value over xG alone. Once you account for shot quality, raw shot counts carry no additional signal. 

Both metrics have modest standalone predictive power (0.58-0.61), which is itself meaningful: first-period possession metrics are necessary but insufficient signals of team quality. Hockey is a high-variance sport and a single period rarely tells the full story.

