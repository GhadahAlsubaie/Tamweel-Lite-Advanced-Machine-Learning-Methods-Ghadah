# Model Card — Tamweel Lite

**Project:** Tamweel Lite
**Training Programme:** SDAIA Academy
**Course:** SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة
**Status:** Ready for review; not a production approval or grading claim

## 1. Model Summary

Tamweel Lite is an educational machine-learning project using synthetic lending data to predict the probability of a simulated default event within 90 days after an application.

The final model selected for the project is **Logistic Regression**.

The final model was selected through forward out-of-fold comparison across three development periods. Logistic Regression achieved the highest mean Average Precision among the evaluated single models and all tested ensemble candidates failed the defined worth-it gate.

**Final decision:** `KEEP SINGLE → Logistic Regression`

## 2. Intended Use

The model is intended for:

* Educational experimentation with supervised machine learning.
* Demonstrating temporal validation, out-of-fold prediction, cost-sensitive decision-making, calibration, and reproducibility.
* Producing a simulated review flag for new application rows.
* Learning how model outputs can support a controlled review workflow.

The output represents a **simulated review flag and probability estimate**.

It is **not** intended to:

* Approve or reject real loan applications.
* Make real financial or credit decisions.
* Determine an individual's creditworthiness in a real-world setting.
* Replace human review or institutional credit policies.
* Provide a fairness, legal, regulatory, or compliance certification.
* Be deployed for real lending decisions without a separate development, validation, governance, and approval process.

## 3. Data

The project uses synthetic course data supplied for the learning exercise.

The dataset contains simulated application and customer-level information. Challenge labels are unavailable and were not used for model development, threshold selection, calibration, or evaluation.

Data roles were separated to reduce leakage risk, including development/fit data, calibration data, policy-selection data, evaluation data, and challenge inference data where applicable.

Customer and temporal separation rules were applied during validation.

No real customer personal data or confidential employer data is intended to be included in the project repository.

## 4. Model Development

Candidate models evaluated during development included:

* LightGBM
* XGBoost
* Logistic Regression
* Equal-weight ensemble
* Weighted ensemble
* Stacking ensemble

Nested forward OOF comparison used **2,155 OOF rows across three forward periods**.

The comparison results were:

| Candidate           |     Mean AP | Fold SD | Mean Brier | Mean ECE |
| ------------------- | ----------: | ------: | ---------: | -------: |
| LightGBM            |     0.34549 | 0.04348 |    0.06608 |  0.02311 |
| XGBoost             |     0.35263 | 0.02904 |    0.06566 |  0.02276 |
| Logistic Regression | **0.39166** | 0.02981 |    0.06327 |  0.01882 |
| Equal Ensemble      |     0.37170 | 0.03258 |    0.06435 |  0.02038 |
| Weighted Ensemble   |     0.38942 | 0.02906 |    0.06332 |  0.01772 |
| Stack Ensemble      |     0.38314 | 0.02949 |    0.06603 |  0.03106 |

All ensemble candidates failed the defined worth-it gate.

Therefore, additional ensemble complexity was not justified.

## 5. Validation Limitations

The reported OOF comparison is based on three forward periods and 2,155 OOF rows.

Fold standard deviation describes variation between the available forward periods; it is **not a confidence interval**.

OOF results were used for development and model selection and should not be interpreted as an untouched final test.

The validation design reduces temporal leakage risk but does not guarantee future performance.

## 6. Decision Policy

The development policy used a cost-sensitive objective of:

**10 × FN + 1 × FP**

with a maximum review capacity of **12% per validation period**, unless an explicitly documented alternative policy is provided.

The selected development threshold was:

`0.168922`

On the corresponding policy evaluation:

* TP: 84
* FP: 161
* FN: 95
* TN: 1,815
* Flagged applications: 245
* Overall flag fraction: 11.369%
* Maximum period flag fraction: 11.749%
* Recall: 0.46927
* Precision: 0.08148
* Capacity feasible: True

The threshold is a learning-exercise policy decision and is not a real lending policy.

## 7. Challenge Inference

The final inference pipeline accepts new feature rows and produces an application identifier, probability, and simulated decision flag.

For the synthetic challenge set:

* Rows: 2,500
* Capacity: 300
* Threshold-eligible rows: 330
* Final flagged rows: 300
* Rows removed by capacity rule: 30
* Boundary score: 0.129908

The batch policy applies the probability threshold first and then ranks eligible applications by probability to enforce the capacity limit.

Challenge labels were unavailable and were not used.

A decision value of `1` represents a **simulated review flag**, not approval or rejection.

## 8. Calibration

Calibration was evaluated separately from model selection.

The project includes calibration diagnostics and reliability analysis. Calibration should be interpreted within the specific data role on which it was fitted and evaluated.

Day 4 evaluation showed improvement in calibration diagnostics after sigmoid calibration, including a reduction in ECE and Brier score. However, the final Day 5 calibration fit diagnostics on the calibration-role rows did not show an improvement in all measures.

Therefore, the project does **not** claim that calibration universally improves model performance.

Calibration quality should be monitored using measures such as:

* Brier score
* Expected Calibration Error (ECE)
* Reliability curves
* Average Precision
* ROC-AUC

Any future recalibration should use newly designated development/calibration data and must not use challenge labels.

## 9. Interpretability

The project includes model interpretability analysis from the Day 4 development workflow.

SHAP values are expressed in **log-odds contribution** for the raw tree-based model used in that analysis. They are not probability points and should not be interpreted directly as percentage-point changes in probability.

The largest global SHAP contributions in the Day 4 analysis included:

* `bureau_score`
* `dti`
* `loan_amount_sar`
* `savings_balance_sar`
* `existing_obligations_sar`

Local explanations were also generated for a synthetic application example.

These explanations describe model behavior. They do **not** establish causality, legal compliance, or fairness.

Importantly, the Day 4 SHAP analysis was performed on a previous tree-based model and should not be interpreted as an explanation of the final Day 5 Logistic Regression model.

## 10. Limitations and Risks

Important limitations include:

* Synthetic educational data may not represent real lending populations or processes.
* Future data distributions may differ from the development data.
* The OOF comparison covers only three forward periods.
* Fold standard deviation is descriptive and is not a confidence interval.
* Calibration results are dataset- and role-specific.
* The challenge set has no available labels for final performance evaluation.
* Capacity constraints can change the resulting review population.
* Model explanations describe model behavior rather than causality.
* Regional analysis is descriptive and is not a fairness certificate.
* No claim of real-world credit performance, regulatory compliance, or production readiness is made.

## 11. Monitoring Recommendations

If this workflow were adapted for a separate legitimate development exercise, monitoring should include:

* Average Precision and ROC-AUC.
* Brier score and ECE.
* Calibration and reliability curves.
* Positive-event prevalence.
* Feature and prediction drift.
* Review-capacity utilization.
* False-positive and false-negative behavior when valid labels become available.
* Stability across future temporal periods.

Any threshold or calibration change should be developed using newly designated development data and evaluated using a separate evaluation role.

The challenge set should not be used to tune the model or policy.

## 12. Reproducibility

The project records:

* Random seed.
* Python environment and package versions.
* Model parameters.
* Data provenance and roles.
* Data and artifact manifests.
* Model artifacts.
* Submission files.
* Git version-control history.

The assessed version should be identified by its final Git tag and full commit SHA.

Re-running the recorded repository version should reproduce the submitted inference outputs subject to the documented environment and reproducibility controls.

## 13. Integrity and Disclosure

This repository is a learner project for the SDAIA Academy course:

**SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة**

Any example or recovery outputs included in the repository are learning aids and must not be presented as personal execution evidence unless they were actually generated by the learner's own execution.

No challenge labels, fabricated metrics, fabricated screenshots, secrets, private credentials, or personal data should be used as evidence.

The repository should not be presented as an official SDAIA repository.

## 14. Overall Model Card Decision

**Final model:** Logistic Regression

**Selection decision:** `KEEP SINGLE`

**Primary selection metric:** Mean Average Precision

**Mean AP:** `0.39166`

**Policy:** Cost-sensitive review flag with `10 × FN + 1 × FP` and a `12%` capacity constraint during development.

**Intended output:** Simulated review probability and review flag.

**Production status:** Not intended for production use.

**Real-world lending decision:** Not supported.

**Overall status:** Educational / experimental model with documented validation, policy, interpretability, calibration, and reproducibility limitations.
