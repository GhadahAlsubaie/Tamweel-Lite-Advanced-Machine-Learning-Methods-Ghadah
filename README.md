# Tamweel Lite — Advanced Machine Learning Methods

An end-to-end machine-learning capstone for cost-sensitive review flagging using synthetic lending data. The project covers temporal validation, out-of-fold probability generation, model comparison, cost-sensitive threshold selection, capacity constraints, calibration diagnostics, interpretability, ensemble evaluation, and final challenge inference.

**Developer:** Ghada
**Project type:** Individual learner project
**Training programme:** SDAIA Academy
**Course:** `SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة`

> **Important:** This repository is a learner project completed for the training programme. It is **not an official SDAIA Academy repository**.

## Project overview

Tamweel Lite addresses a simulated lending-review decision:

> **Which applications should be flagged for human review when missing a risky application is substantially more costly than reviewing a non-risky application, while review capacity is limited?**

The project uses synthetic course data and applies a cost-sensitive decision policy with:

* **False Negative cost = 10**
* **False Positive cost = 1**
* **Maximum review capacity = 12% per validation period**

The system produces a **review flag**, not a loan approval or rejection decision.

The complete workflow covers:

1. Data-role validation and provenance
2. Temporal/customer-aware dataset separation
3. Baseline and candidate model development
4. Forward out-of-fold probability generation
5. Logistic Regression, XGBoost, and LightGBM comparison
6. Ensemble evaluation through a documented Worth-It Gate
7. Cost-sensitive threshold selection
8. Capacity-constrained review policy
9. Calibration diagnostics
10. Interpretability and stability analysis
11. Final model refit
12. Challenge-set inference without challenge labels
13. Reproducibility and provenance artifacts

---

## Key decision and value

The project is designed to improve one specific operational decision:

**Select a limited set of applications for review while balancing the cost of missed risk against unnecessary review.**

The decision policy uses the course-defined loss:

`10 × FN + 1 × FP`

subject to a maximum review capacity of **12% per validation period**.

This makes the final output a **review-prioritisation signal**, rather than a prediction of approval, rejection, or legal creditworthiness.

---

# Key results

## Model comparison

Model comparison was performed using **2,155 nested out-of-fold predictions across three forward validation periods**.

The primary comparison metric was Average Precision (AP), with fold stability, Brier score, ECE, and the documented ensemble Worth-It Gate also considered.

| Model                   |     Mean AP | Fold SD | Mean Brier | Mean ECE | Lift vs single |
| ----------------------- | ----------: | ------: | ---------: | -------: | -------------: |
| **Logistic Regression** | **0.39166** | 0.02981 |    0.06327 |  0.01882 |    **0.00000** |
| Weighted Ensemble       |     0.38942 | 0.02906 |    0.06332 |  0.01772 |       -0.00224 |
| Stacking                |     0.38314 | 0.02949 |    0.06603 |  0.03106 |       -0.00852 |
| Equal Ensemble          |     0.37170 | 0.03258 |    0.06435 |  0.02038 |       -0.01996 |
| XGBoost                 |     0.35263 | 0.02904 |    0.06566 |  0.02276 |       -0.03903 |
| LightGBM                |     0.34549 | 0.04348 |    0.06608 |  0.02311 |       -0.04617 |

### Final model decision

**KEEP SINGLE → Logistic Regression**

None of the evaluated ensembles passed the documented Worth-It Gate. Logistic Regression therefore remained the selected single model.

See:

* [Ensemble Decision](reports/ENSEMBLE_DECISION.md)
* [Ensemble gate evidence](artifacts/day5_ensemble_gate.json)
* [Fold scores](artifacts/day5_fold_scores.csv)
* [OOF predictions](artifacts/day5_oof_predictions.csv)

---

## Validation strategy

The project uses temporal and customer-aware role separation to reduce leakage risk.

The Day 5 development workflow contained:

| Role             |  Rows | Customers | Positives | Period                  |
| ---------------- | ----: | --------: | --------: | ----------------------- |
| Fit + selection  | 6,576 |     3,931 |       537 | 2022-01-01 → 2024-04-01 |
| Calibration only |   836 |       788 |        78 | 2024-07-01 → 2024-09-30 |
| Excluded         | 2,588 |         — |         — | —                       |

The nested OOF process produced:

* **2,155 OOF rows**
* **3 forward validation periods**
* Warm-up rows without an outer OOF prediction were excluded from the OOF comparison.

OOF development/selection is not treated as an untouched final test set.

See:

* [Day 5 roles](artifacts/day5_roles.csv)
* [OOF provenance](artifacts/day5_oof_provenance.json)
* [OOF predictions](artifacts/day5_oof_predictions.csv)
* [Final provenance](artifacts/day5_final_provenance.json)

---

# Cost-sensitive review policy

The selected threshold was:

`0.168922`

Using the course policy of **10×FN + 1×FP**, the OOF decision produced:

| Metric                       |   Result |
| ---------------------------- | -------: |
| Threshold                    | 0.168922 |
| TP                           |       84 |
| FP                           |      161 |
| FN                           |       95 |
| TN                           |    1,815 |
| Review flags                 |      245 |
| Flag fraction                |   11.37% |
| Maximum period flag fraction |   11.75% |
| Recall                       |   46.93% |
| Precision                    |    8.15% |
| Loss units                   |    1,111 |
| Capacity feasible            |      Yes |

The maximum observed period flag fraction was **11.75%**, remaining below the **12% capacity constraint**.

See:

* [Decision Card](reports/DECISION_CARD.md)
* [Final policy](artifacts/final_policy.json)
* [Threshold sweep](artifacts/day5_threshold_sweep.csv)
* [Period capacity audit](artifacts/day5_period_capacity.csv)
* [Regional audit](artifacts/day5_region_audit.csv)

### Cost sensitivity

With the threshold fixed, the observed loss changed as the FN cost changed:

| FN cost | FP cost |  Loss |
| ------: | ------: | ----: |
|       8 |       1 |   921 |
|      10 |       1 | 1,111 |
|      12 |       1 | 1,301 |

This sensitivity analysis is descriptive and does not replace the course-defined `10 × FN + 1 × FP` policy.

---

# Challenge inference

The challenge labels were unavailable and were **not used**.

Final inference was run on:

* **2,500 challenge rows**
* Capacity: **300 applications**
* Initially threshold-eligible: **330**
* Final flags after capacity rule: **300**
* Removed by capacity: **30**
* Capacity utilisation: **12%**

The final batch policy was:

1. Generate probabilities using the frozen final model.
2. Apply the frozen threshold.
3. Rank threshold-eligible applications by probability.
4. Enforce the final capacity limit.
5. Retain complete equal-score blocks according to the documented tie policy.

`decision = 1` represents a **simulated review flag only**.

Because challenge labels were unavailable, **no challenge-set accuracy, AP, ROC-AUC, recall, precision, or loss is reported**.

See:

* [Submission](submission/submission.csv)
* [Submission manifest](submission/submission_manifest.json)
* [Challenge capacity evidence](artifacts/day5_challenge_capacity.png)
* [Final provenance](artifacts/day5_final_provenance.json)

---

# Calibration

Calibration was evaluated separately from model selection.

The Day 5 calibration fit used **836 calibration rows**.

The fit diagnostics showed:

| Metric            |   Before |    After |
| ----------------- | -------: | -------: |
| ROC-AUC           | 0.789037 | 0.789037 |
| Average Precision | 0.789037 | 0.789037 |
| ECE               | 0.266489 | 0.277296 |
| Log Loss          | 0.076473 | 0.078058 |
| Brier             | 0.287803 | 0.287803 |

Calibration therefore **did not demonstrate improvement in these fit diagnostics**:

* ECE increased slightly.
* Log Loss increased slightly.
* Brier score remained unchanged.
* ROC-AUC and AP remained unchanged.

These diagnostics are not treated as an independent final evaluation because the calibration rows were used to fit the calibration mapping.

See:

* [Calibration fit predictions](artifacts/day5_calibration_predictions.csv)
* [Calibration fit bins](artifacts/day5_calibration_fit_bins.csv)
* [Calibration fit evidence](artifacts/day5_calibration_fit.png)
* [Calibration metrics](artifacts/calibration_metrics.json)

---

# Interpretability

The repository contains a Day 4 interpretability analysis based on an **earlier model**, not the final Day 5 Logistic Regression model.

This distinction is intentional and documented.

The Day 4 analysis included:

* permutation importance
* global SHAP
* local SHAP
* stability analysis
* calibration analysis

Example local explanation:

**TR-009585**

* Raw score: `0.9031`
* Calibrated probability: `0.4795`
* `bureau_score` contribution: `+2.269325` log-odds
* `dti` contribution: `+1.198145` log-odds
* `loan_amount_sar` contribution: `+0.200725` log-odds

The SHAP analysis is **descriptive, not causal**. It is not presented as a fairness certificate, legal-compliance assessment, or causal explanation.

See:

* [Interpretability Report](reports/INTERPRETABILITY_REPORT.md)
* [Global SHAP](artifacts/day4_shap_global.csv)
* [SHAP metadata](artifacts/day4_shap_metadata.json)
* [SHAP example](day4_shap_example.json)
* [Permutation importance](artifacts/permutation_importance.csv)

---

# Stability and monitoring

The project includes period-level stability analysis and customer-cluster bootstrap analysis.

Monitoring considerations for future development data include:

* Average Precision
* Brier score
* Expected Calibration Error
* calibration stability
* score and probability drift
* prevalence changes
* false-positive / false-negative trade-offs
* review capacity
* threshold stability

A future threshold or model change should be evaluated on new development evidence rather than tuned on the unlabeled challenge set.

See:

* [Stability summary](artifacts/day4_stability_summary.json)
* [Bootstrap results](artifacts/day4_bootstrap.csv)
* [Period metrics](artifacts/day4_period_metrics.csv)

---

# Technical pipeline

1. Verify the synthetic course data and data roles.
2. Separate fit, selection, calibration, policy, and evaluation roles according to the documented temporal workflow.
3. Build candidate models using training/development data only.
4. Generate nested forward OOF probabilities.
5. Compare Logistic Regression, XGBoost, and LightGBM.
6. Evaluate equal-weighted, weighted, and stacked ensembles.
7. Apply the documented Worth-It Gate.
8. Select Logistic Regression as the final single model.
9. Evaluate the cost-sensitive threshold under the 12% capacity constraint.
10. Perform calibration diagnostics on the designated calibration rows.
11. Refit the selected final model according to the documented final workflow.
12. Generate challenge probabilities without using challenge labels.
13. Apply the frozen threshold and capacity policy.
14. Produce `submission.csv` and provenance artifacts.
15. Preserve the final repository state for reproducibility and assessment.

---

# Repository structure

```text
Tamweel-Lite-Advanced-Machine-Learning-Methods-Ghadah/
├── artifacts/
│   ├── final_model/
│   │   ├── model.json
│   │   └── model_manifest.json
│   ├── day5_ensemble_gate.json
│   ├── day5_oof_predictions.csv
│   ├── day5_oof_provenance.json
│   ├── day5_final_provenance.json
│   ├── final_metrics.json
│   ├── final_policy.json
│   ├── environment.json
│   └── ...
├── data/
├── presentation/
│   └── final_presentation.pdf
├── reports/
│   ├── DECISION_CARD.md
│   ├── ENSEMBLE_DECISION.md
│   ├── INTERPRETABILITY_REPORT.md
│   └── MODEL_CARD.md
├── submission/
│   ├── submission.csv
│   └── submission_manifest.json
├── Tamweel_Lite v2.ipynb
├── data_contract.json
├── data_manifest.json
├── feature_dictionary.csv
├── tamweel_challenge.csv
├── tamweel_dirty.csv
├── tamweel_oof_matrix.csv
├── tamweel_train.csv
├── day4_shap_example.json
├── day4_shap_example.npz
├── day5_oof_example.json
├── LICENSE
└── README.md
```

---

# Quick start

## 1. Environment

The project was developed and executed using:

* Google Colab Free CPU
* Python `3.13.16`
* `seed = 211`
* `n_jobs = 2`
* `FAST_MODE = True`

No paid compute, external API, GPU, or Google Drive dependency is required for the documented learner workflow.

The exact environment and package information are recorded in:

* [Environment manifest](artifacts/environment.json)

## 2. Run the learner notebook

Open:

[Tamweel_Lite v2.ipynb](Tamweel_Lite%20v2.ipynb)

The notebook consolidates the five course days into one learner notebook:

```text
# Day 1 — Baseline Boosting
# Day 2 — Validation & Tuning
# Day 3 — Imbalance, OOF Probabilities, Cost-Sensitive Decisions
# Day 4 — Explainability, Calibration, and Stability
# Day 5 — Ensemble, Final Model, and Challenge Inference
```

The notebook contains executed outputs from the learner's project run.

For assessment, the repository should be treated as the reproducible final project version identified by the final Git commit and tag.

---

# Data and provenance

The project uses **synthetic course data**.

Challenge labels were unavailable and were not used for:

* training
* model selection
* threshold selection
* calibration
* evaluation
* performance reporting

The repository documents data roles, provenance, and reproducibility information.

See:

* [Data contract](data_contract.json)
* [Data manifest](data_manifest.json)
* [Feature dictionary](feature_dictionary.csv)
* [Final provenance](artifacts/day5_final_provenance.json)
* [OOF provenance](artifacts/day5_oof_provenance.json)

---

# Reports and assessment evidence

The project includes the four required technical reports:

### Decision Card

[Open Decision Card](reports/DECISION_CARD.md)

Summarises the final decision, threshold, capacity, loss policy, and intended use.

### Interpretability Report

[Open Interpretability Report](reports/INTERPRETABILITY_REPORT.md)

Documents the Day 4 explanation scope, SHAP analysis, limitations, and stability evidence.

### Ensemble Decision

[Open Ensemble Decision](reports/ENSEMBLE_DECISION.md)

Documents the Worth-It Gate and the decision to keep a single Logistic Regression model.

### Model Card

[Open Model Card](reports/MODEL_CARD.md)

Documents intended use, limitations, validation, data scope, and model risks.

### Final presentation

[Open final presentation PDF](presentation/final_presentation.pdf)

The presentation contains five slides covering:

1. Problem and value
2. Trusted method
3. Evidence and model decision
4. Interpretation, calibration, and limitations
5. Final decision and follow-up

---

# Reproducibility

Reproducibility is treated as a first-class project requirement.

The repository records:

* random seed
* package/environment information
* data roles
* model parameters
* validation structure
* provenance
* output artifacts
* model files
* submission manifest
* final repository version

The assessed version should be identified using:

* final Git commit
* full commit SHA
* final Git tag
* preserved manifest/bundle

No assessed history should be rewritten after finalisation.

---

# Intended use

This project is an **educational and experimental Tamweel Lite modelling exercise** using synthetic data.

The final output is intended to:

* prioritise applications for simulated human review
* demonstrate cost-sensitive classification
* demonstrate capacity-constrained decision policies
* demonstrate reproducible machine-learning workflow

The output is **not intended to**:

* approve or reject loans
* make legally binding credit decisions
* establish real-world creditworthiness
* replace human review
* provide a fairness or legal-compliance certification
* make causal claims from model explanations

---

# Limitations

Important limitations include:

* The project uses synthetic course data.
* The challenge labels are unavailable.
* Therefore, challenge-set performance cannot be measured honestly.
* OOF development/selection is not an untouched final test set.
* Calibration fit diagnostics are not an independent evaluation of calibration performance.
* Day 4 SHAP explanations correspond to an earlier model and should not automatically be interpreted as explanations of the final Day 5 Logistic Regression model.
* Regional audits are descriptive and are not presented as fairness certificates.
* The capacity policy is demonstrated on the available validation evidence and should be re-evaluated with new development data.
* Real-world deployment would require independent data, governance, monitoring, and domain validation.

---

# Assessment integrity

This repository follows the project integrity requirements:

* No challenge labels are used.
* No fabricated metrics, screenshots, timestamps, commits, or execution evidence are presented.
* No personal or confidential employer data is included.
* Recovery/example outputs are treated as learning aids and are not presented as personal execution evidence.
* Model explanations are appropriately scoped.
* Challenge predictions are not presented as challenge performance.
* The final repository version must be identified by its final commit and Git tag.

---

# Training-program attribution

This project was completed for:

**SDAIA Academy — Advanced Machine Learning Methods**

**Course:** `SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة`

Training-program reference:

[SDAIA Academy GitHub](https://github.com/SDAIAAcademy)

This is an independent learner repository and is **not an official SDAIA Academy repository**.

---

# AI assistance disclosure

AI tools were used as learning and development assistance where appropriate, including support with:

* code explanation and debugging
* documentation drafting
* repository organisation
* interpretation of technical outputs
* wording and presentation preparation

The project evidence, executed outputs, modelling decisions, validation results, and final submission artifacts are based on the learner's own project execution.

---

# Final repository status

The final assessed version should record:

| Item                | Final value                                                            |
| ------------------- | ---------------------------------------------------------------------- |
| Repository          | `GhadahAlsubaie/Tamweel-Lite-Advanced-Machine-Learning-Methods-Ghadah` |
| Final commit        | To be recorded after final review                                      |
| Full commit SHA     | To be recorded after final review                                      |
| Final Git tag       | To be recorded after final review                                      |
| Submission manifest | `submission/submission_manifest.json`                                  |
| Submission file     | `submission/submission.csv`                                            |
| Final presentation  | `presentation/final_presentation.pdf`                                  |

**Do not create the final tag until all required files, README content, reports, presentation, submission artifacts, and repository checks have been completed.**

---

## Final technical decision

**KEEP SINGLE → Logistic Regression**

The selected model achieved the strongest mean OOF Average Precision among the evaluated candidates:

`Mean AP = 0.39166`

All evaluated ensemble candidates failed the documented Worth-It Gate.

The final review policy uses:

`Threshold = 0.168922`

under:

`10 × FN + 1 × FP`

with a maximum review capacity of:

`12%`

The final challenge inference produced:

`300 review flags / 2,500 challenge rows`

with no challenge performance claims because challenge labels were unavailable.
