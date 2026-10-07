# Tamweel Lite — Advanced Machine Learning Methods

An educational machine learning project developed as part of the **SDAIA Academy — Advanced Machine Learning Methods** training programme.

> **Course:** `SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة`

## Project Purpose

Tamweel Lite demonstrates an end-to-end machine learning workflow for **credit-risk prioritization and simulated review screening** using synthetic course data.

The project focuses on:

* classification model comparison
* time-aware validation
* out-of-fold (OOF) probability generation
* cost-sensitive threshold selection
* capacity-constrained decision policies
* calibration diagnostics
* model stability
* interpretability
* ensemble evaluation
* reproducible final inference

The project is **educational and experimental**. It does not represent a real lending system or a real credit decision.

---

## Problem

The project addresses a binary classification problem in which applications are assigned estimated risk probabilities and then prioritized for a simulated review process.

The workflow evaluates models beyond ROC-AUC by considering:

* Average Precision
* time-aware validation
* out-of-fold probabilities
* threshold selection
* simulated decision loss
* review-capacity constraints
* calibration diagnostics
* model stability
* interpretability
* operational complexity

The educational decision policy uses:

`10 × FN + 1 × FP`

with a simulated review-capacity ceiling of **12%**.

The cost values are educational units only. They do not represent Saudi Riyal losses, expected credit loss, or real financial costs.

---

## Project Workflow

The course work is maintained in a **consolidated learner notebook** covering Day 1 through Day 5, following the instructor's delivery instruction to place the course work in one notebook with clear day headings.

### Day 1 — Baseline Boosting

* Data preparation
* Baseline classification modelling
* Initial model comparison

### Day 2 — Validation & Tuning

* Time-aware validation
* Out-of-fold predictions
* Model validation and tuning

### Day 3 — Imbalance, OOF Probabilities, Cost-Sensitive Decisions

* Class imbalance
* OOF probability analysis
* Cost-sensitive threshold selection
* Capacity-aware decision rules

### Day 4 — Explainability, Calibration, and Stability

* Model explainability
* SHAP analysis
* Calibration diagnostics
* Stability and diagnostic analysis

### Day 5 — Ensemble, Final Model, and Challenge Inference

* Ensemble comparison
* Worth-It Gate
* Final model selection
* Frozen threshold application
* Capacity-constrained simulated review screening
* Challenge inference

---

## Data

The project uses **synthetic course data only**.

No confidential employer data, third-party personal data, credentials, or real customer lending records are used.

The challenge dataset is intentionally **unlabeled**. Challenge labels are not used or inferred.

The workflow maintains separate roles for model development, calibration, policy selection, and challenge inference to reduce leakage and preserve the intended evaluation design.

---

## Models

The project evaluates:

* Logistic Regression
* XGBoost
* LightGBM

Ensemble alternatives were also evaluated using:

* Equal-weight ensemble
* Weighted ensemble
* Stacking

### Final Model Decision

The final decision was:

**KEEP SINGLE → Logistic Regression**

Logistic Regression achieved the highest mean Average Precision among the single-model candidates:

| Candidate             | Mean Average Precision |
| --------------------- | ---------------------: |
| Logistic Regression   |            **0.39166** |
| Weighted Ensemble     |                0.38942 |
| Stacking              |                0.38314 |
| Equal-weight Ensemble |                0.37170 |
| XGBoost               |                0.35263 |
| LightGBM              |                0.34549 |

None of the ensemble alternatives passed the project's **Worth-It Gate**. Therefore, the additional complexity of an ensemble was not justified by the observed validation evidence.

Choosing a single model was an evidence-based decision rather than an assumption that ensembles are always better.

---

## Validation

Model comparison used **2,155 OOF rows across three forward validation periods**.

The OOF predictions were used for model comparison and decision-policy development.

OOF results are not treated as an untouched final test set. The observed fold variation does not represent a confidence interval, and validation performance cannot guarantee future production performance.

The validation design uses forward temporal separation to reduce look-ahead leakage.

---

## Decision Policy

The educational decision policy uses:

`10 × FN + 1 × FP`

with a maximum review-capacity ceiling of **12%**.

The selected raw threshold was:

`0.168922`

The threshold was selected using OOF development evidence rather than the unlabeled challenge batch.

### Challenge Screening

The final model was applied to **2,50**
