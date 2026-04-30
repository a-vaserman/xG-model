# NHL First-Period xG vs. Shot Volume: Predicting Game Outcomes
Does shot quality beat shot quantity at predicting NHL winners and how early can we tell?

This project builds a custom expected goals (xG) model from shot-level data and compares its predictive power against raw shot volume using only **first-period 5v5 data** across 3,936 regular season games (2022-2024).

---

## Finding
| Model | AUC |
|---|---|
| Shot Ratio (Corsi) | 0.585 |
| xG Ratio | 0.609 |
| Combined | 0.608 |

**xG outperforms shot volume by 2.4 AUC points.** More notably, combining both metrics adds no predictive value over xG alone. Once you account for shot quality, raw shot counts carry no additional signal. 

Both metrics have modest standalone predictive power (0.58-0.61), which is itself meaningful: first-period possession metrics are necessary but insufficient signals of team quality. Hockey is a high-variance sport and a single period rarely tells the full story.

<img width="1200" height="900" alt="roc_curve" src="https://github.com/user-attachments/assets/8a63cd5a-33af-4fe6-bfd1-fce76334bc9e" />

---

## How It Works

### Step 1: Build a Custom xG Model

MoneyPuck pre-computed xG values were unavailable in this dataset, so xG was derived from first principles using shot features:
- **Shot distance to goal** (calculated from x/y coordinates)
- **Shot angle** to goal
- **Shot type** (wrist, backhand, tip-in, slap, etc.)
- **Rebound flag**

A logistic regression model trained on 400,000+ 5v5 shot attempts achieved **AUC = 0.801** at predicting whether a shot results in a goal - comparable to published xG models.

### Step 2: Aggregate to Game Level
For each game, first-period 5v5 shots are aggregated into two ratio features:
- `home_shot_ratio` = home shots / total shots
- `home_xg_ratio` = home xG / total xG

Ratios are used rather than raw counts to control for variation in game pace.

### Step 3: Compare Predictive Models
Three logistic regression models are evaluated via 5-fold cross-validation predicting binary game outcome (home win / loss):

1. Shot ratio only
2. xG ratio only
3. Both combined

---

### Data

- **Source:** NHL API + MoneyPuck (2022, 2023, 2024 regular seasons)
- **Scope:** 3,936 regular season games, ~400,000 5v5 shot attempts
- **Filtered to:** 5v5 strength state only (excludes power plays, pulled goalie, overtime)
- **Storage:** PostgreSQL

---

## Stack

- **Python** - pandas, scikit-learn, matplotlib, SQLAlchemy
- **PostgreSQL** - shot-level and game-level data
- **Models** - Logistic Regression (sklearn)

---

## Limitations

The conclusion that xG outperforms Corsi is consistent with established hockey analytics literature (MoneyPuck, Evolving Hockey, Manny Perry et al.). This project's contribution is a **reproducible implementation** with a specific first-period temporal framing and a from-scratch xG model built without pre-computed values.

Known constraints:
- `is_rush` was unpopulated in this dataset and excluded from the xG model
- First-period sample sizes are small relative to full-game data - predictive power would increase using full-game metrics
- No goalie quality adjustment - save percentage variance is a meaningful cofounder not captured here
- Three seasons is sufficient for team-level analysis but limits individual player inference

---

## Files

| File | Description |
|---|---|
| `nhl_xg_model.py` | Full pipeline: xG model, game aggregation, logistic regression comparison, ROC curve |
| `ROC Curve (NHL).png` | ROC curve comparing all three models |
