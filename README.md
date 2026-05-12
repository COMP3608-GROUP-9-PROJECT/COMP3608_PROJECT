# COMP3608 Project

## COMP 3608 Intelligent Systems

This repository contains our project for **Kaggle's March Machine Learning Mania 2025** competition. The goal was to predict the outcomes of both the Men's and Women's `2025` NCAA Division I basketball tournaments by estimating the probability that the team with the lower `TeamId` defeats the team with the higher `TeamId` in every possible matchup.

Using historical regular season results, tournament data, team seedings, and engineered performance features, we developed and compared three machine learning models:

- Logistic Regression
- Random Forest
- XGBoost

All submissions were evaluated using the **Brier Score**, the official Kaggle competition metric, where lower values indicate more accurate and better calibrated probability predictions.

### Competition Objective

The task was to use historical NCAA game data to forecast tournament game outcomes as probabilities rather than simple win/loss labels. This makes the challenge a probability estimation problem evaluated on calibration and accuracy.

### Evaluation Metric

Submissions were evaluated using the **Brier Score**, which in this context is equivalent to mean squared error between predicted probabilities and actual outcomes.

`Brier Score = (1/N) * sum((y_i - p_i)^2)`

### Submission Format

The submission file combined both Men's and Women's tournaments into a single CSV. Each row used a unique game ID in the form:

`Season_Team1_Team2`

Example:

```csv
ID,Pred
2025_1101_1102,0.5
2025_1101_1103,0.5
2025_1101_1104,0.5
```

Here, `Pred` represents the predicted probability that the first team listed in the ID, which is the team with the lower `TeamId`, wins the matchup.


## Group Members

| ID | Name |
| --- | --- |
| 816039998 | Michel Sanchez Aldana |
| 816030494 | Tyreece Hamilton |
| 816039902 | Kelly Ann Boodram |

## Problem Identification

The NCAA Basketball Tournament is one of the most prestigious collegiate sporting events held annually in the United States, where Division I Men's and Women's teams compete for national recognition. Accurately forecasting tournament outcomes is challenging because game results depend on many variables including historical team performance, seedings, relative strength, and situational game factors.

This project uses historical NCAA data from the Kaggle March Machine Learning Mania 2025 competition to build predictive models for tournament games. The goal is to estimate the probability of victory for every possible matchup in the 2025 Men's and Women's tournaments and compare model quality using Kaggle's Brier Score metric.

## Problem Classification and Model Selection

This is a **probabilistic binary classification** problem. For each tournament matchup, the model predicts a value between `0` and `1` representing the probability that `Team 1` wins.

### Logistic Regression

Logistic Regression was used as a baseline because it is simple, interpretable, and efficient to train. It also provides a useful reference point for determining whether more advanced non-linear models are justified.

### Random Forest

Random Forest was selected because it performs well on structured tabular data and can model non-linear feature interactions. By averaging the predictions of many decision trees trained on bootstrap samples, it can capture patterns that a linear model may miss.

### XGBoost

XGBoost was selected as the strongest candidate model for this project due to its strong performance on tabular data and its ability to learn complex non-linear relationships. Unlike Random Forest, XGBoost builds trees sequentially so that each tree corrects the errors of the previous one. It also includes regularization that helps reduce overfitting and supports richer training signals such as signed point margin.


### Model Objectives

- **Logistic Regression:** optimizes regularized logistic loss
- **Random Forest:** uses entropy-based splits to reduce impurity
- **XGBoost:** optimizes an additive objective combining training loss with regularization

## Datasets and Experimental Design

Three real-world datasets from the Kaggle March Machine Learning Mania 2025 competition were used. All contain historical NCAA basketball data from `2003` onward.

### Dataset 1: Regular Season Detailed Results

This was the main source for feature engineering. It contains box score statistics for every regular season game across both Men's and Women's divisions. Each row includes the winning and losing team, final scores, and 13 detailed statistics per team such as field goals, rebounds, assists, turnovers, steals, blocks, and fouls.

After combining both divisions, this dataset contains **200,590 rows** across **35 columns**. It was used to compute season averages, opponent-allowed statistics, and Elo ratings for each team.

### Dataset 2: NCAA Tournament Detailed Results

This dataset has the same box score structure as the regular season data but includes tournament games only. After combining both divisions, it contains **2,276 rows** from `2003` to `2024`.

It serves as the labelled dataset for training and validating the models.

### Dataset 3: NCAA Tournament Seeds

This dataset contains official tournament seedings such as `W01` or `X16`. After combining both divisions and filtering to seasons from `2003` onward, it contains **2,896 rows**.

Seeds were converted into integers and used directly as features, along with engineered differences such as `SeedDifference`.

## Experimental Pipeline

### 1. Data Preprocessing

Men's and Women's datasets were combined and tagged with a division flag. Game statistics were normalized to a standard 40-minute regulation game using a pace factor that accounts for overtime. Each game was duplicated to create symmetric `Team 1` and `Team 2` pairings so the models could learn from both winning and losing perspectives.

### 2. Feature Engineering

Four main categories of features were engineered:

- Season averages across 28 statistical categories
- Opponent-allowed defensive statistics
- Elo ratings simulated independently for each season
- GLM performance grades controlling for opponent strength

Tournament seeds and difference-based features were then merged onto both sides of each matchup.

### 3. Training

All three models were trained on the processed tournament dataset derived from the engineered regular season and tournament features.

### 4. Prediction

The prediction set consisted of all **131,407** possible pairings of teams appearing in the `2025` season, based on `SampleSubmissionStage2.csv`. The same feature engineering pipeline used for training was applied to generate prediction features.

### 5. Evaluation

Model performance was evaluated using the **Brier Score**, the official Kaggle competition metric.

## Implementation

### Project Links

- Trello board: <https://trello.com/b/WHpfiBT0/comp3608-group-9-project>
- GitHub repository: <https://github.com/COMP3608-GROUP-9-PROJECT/COMP3608_PROJECT>

### Notebooks

- Data preprocessing notebook: https://colab.research.google.com/drive/1UsjAn2S4nJOiNDKJ4_v6c1dPUy4XewjL?usp=sharing 

- Logistic Regression notebook: https://colab.research.google.com/drive/1kSGL4LJ9NaW-sClpN6nTZ76Suw8RukBE?usp=sharing

- Random Forest notebook: https://colab.research.google.com/drive/1x_1LmqChbJVVGv0fOXx0Q4Pj10q0eVyK?usp=sharing

- XGBoost notebook: [XGBoost/XGBoost_model.ipynb](./XGBoost/XGBoost_model.ipynb)

## Results and Discussion

All three models produced reasonable tournament win probabilities, but **XGBoost** achieved the strongest overall performance.

### XGBoost

XGBoost achieved the best result with a **Kaggle private score of 0.10786**, placing the team in the **top 10** of the private leaderboard.

This result was produced using **46 features**, including:

- Tournament seeds
- Elo ratings
- GLM performance grades
- Season box score averages across both divisions

Feature contribution was also evaluated using single-feature and reduced-feature submissions:

| Feature Set | Kaggle Private Score |
| --- | --- |
| `T1Seed` only | `0.18087` |
| `SeedDifference` only | `0.13869` |
| `GradeDifference` only | `0.12570` |
| Top 3 combined | `0.12457` |
| Full 46-feature model | `0.10786` |

Compared with the `T1Seed` baseline:

- `GradeDifference` improved the Brier Score by **30.5%**
- `SeedDifference` improved it by **23.3%**
- The full model improved it by **40.4%**

The top-3 combined feature set improved on the `T1Seed` baseline by **31.1%**, but still underperformed the full model by **13.7%**, showing that the broader feature set contributed additional predictive value.

The model was validated using **leave-one-season-out cross-validation** across 21 seasons. Probability calibration was performed using a **degree-5 univariate spline** fitted on out-of-fold predicted margins, and final predictions were averaged across all trained seasonal models.

### Logistic Regression

Logistic Regression achieved a **Kaggle private score of 0.11447**, making it the second-best model. This was notable because the expectation was that it would perform worst due to the likely non-linear nature of the problem.

One possible explanation is that **L2 regularization** helped control overfitting better than Random Forest. A reduced model using only the top three features produced a score of **0.21430**, showing that the full feature set significantly improved predictive performance.

### Random Forest

Random Forest achieved a **Kaggle private score of 0.11769** using all features and **0.11836** using only the top three features. The full feature set performed slightly better, suggesting that while seedings, Elo ratings, and grades are the strongest features, additional box score statistics still contributed useful predictive information.

A likely limitation is that the model relied on season averages, which do not capture late-season momentum, injuries, or recent form. Also, while hyperparameters were tuned using `RandomizedSearchCV`, a full `GridSearchCV` may have yielded a stronger configuration.

## Conclusion

The project successfully developed and compared three intelligent systems for NCAA tournament prediction. Among them, **XGBoost** produced the best calibrated predictions and achieved a **top 10 Kaggle private leaderboard result**, making it the strongest overall solution for this task.
