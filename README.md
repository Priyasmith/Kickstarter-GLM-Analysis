# Decoding Crowdfunding Success: A Statistical Journey Through Kickstarter Data

A dual-model econometric framework built in R leveraging Generalized Linear Models (GLMs) to forecast campaign survival boundaries and model heavily overdispersed backer counts.

---

## 📌 Project Overview
Reward-based crowdfunding campaigns on platforms like Kickstarter exhibit extreme volatility, positive skewness, and severe zero-inflation. This project investigates the structural mechanics governing campaign viability by decoupling binary survival from the subsequent scale of crowd participation.

The study tackles the problem through two primary statistical frameworks:
1. **Predictive Binary Classification:** Evaluates pre-launch structural viability while eliminating post-treatment data leakage (endogeneity).
2. **Count Data Modeling:** Resolves extreme overdispersion and structural failure mass using a mixture distribution architecture.

---

## 🔬 Methodological Framework

### 1. Data Engineering & Diagnostics
* **State Filtration:** Removed transitional and administrative states (`live`, `undefined`) to define a strictly dichotomous Bernoulli response space ($N = 372,300$).
* **Variance Stabilization:** Applied offset logarithmic transformations (log(1 + x)) to financial goals and backer counts to mitigate extreme leverage from heavy-tailed outliers.
* **Feature Engineering:** Extracted *a priori* predictors including campaign duration, temporal launch seasonality (`launch_month`), title character length (`name_length` as marketing effort proxy), and geographic indicators (`is_US`).

### 2. Binary Classification: Logistic Regression
* **Data Leakage Resolution:** Demonstrated that including concurrent backer counts creates near-circularity ($\text{AUC} \approx 0.98$). Formulated a strictly pre-launch, leak-free model relying solely on structural day-one decisions.
* **Micro-Market Heterogeneity:** Expanded overarching industry sectors into granular sub-categories (150+ classes), resolving severe Omitted Variable Bias.
* **Out-of-Sample Performance:** Evaluated on an independent 20% hold-out test set ($N = 74,460$), achieving an **AUC of 0.7239** and a **True Negative Rate (Specificity) of 85.28%**.

### 3. Scale of Success: Zero-Inflated Poisson (ZIP) Regression
* **Overdispersion Pathology:** Identified a Variance-to-Mean Ratio (VMR) of **7,792.13**, violating standard Poisson equidispersion ($E[Y] = \text{Var}[Y]$) due to a massive influx of campaigns failing to secure any backers.
* **Mixture Architecture:** Deployed a two-part model via the `pscl` package:
  * **Logistic Component:** Models the probability ($\pi_i$) of structural failure ("dead-on-arrival" zero-mass).
  * **Poisson Component:** Models the conditional intensity ($\lambda_i$) for projects that penetrate the initial barrier to entry.
* **Zero-Catch Metric:** Successfully forecasted **10,297 structural zeros** against an empirical test-set reality of **10,263** (>99.6% zero-catch precision).

---

## 📊 Summary of Model Evolution

| Model Architecture | Covariate Space | Accuracy | Sensitivity | Specificity | AUC |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **1. Explanatory (Leaky)** | Goal, Duration, Category, Backers | 93.08% | 89.36% | 95.24% | 0.9798 |
| **2. A Priori (Baseline)** | Goal, Duration, Category | 66.72% | 29.66% | 87.47% | 0.6835 |
| **3. Maximized A Priori** | Goal, Duration, Sub-Category, Seasonality, Country, Name Length | **69.10%** | **40.19%** | **85.28%** | **0.7239** |

---

## 🛠️ Tech Stack & Dependencies
* **Language:** R
* **Core Libraries:** 
  * Data Processing: `tidyverse`, `lubridate`, `readxl`
  * Econometric & Count Modeling: `pscl`
  * Evaluation & Diagnostics: `pROC`, `corrplot`, `ggplot2`

---
