# Decision Card — Tamweel Lite

## 1. Decision Summary

**Final model decision:** KEEP SINGLE MODEL → Logistic Regression

The ensemble Worth-It Gate did not justify replacing the single Logistic Regression model. Logistic Regression achieved the highest mean out-of-fold Average Precision among the evaluated candidates and remained the reference single model for the final decision.

* Mean OOF AP: **0.39166**
* Fold standard deviation: **0.02981**
* OOF rows: **2,155**
* Forward validation periods: **3**
* Ensemble candidates evaluated: Equal, Weighted, and Stack
* Gate result: **No ensemble passed**
* Final decision: **KEEP SINGLE MODEL → Logistic Regression**

## 2. Validation Design

The final model-selection evidence was based on nested forward out-of-fold predictions.

* OOF observations: **2,155**
* Forward periods: **3**
* Warm-up observations were not assigned outer OOF predictions.
* OOF predictions were used for model comparison and policy/threshold development.
* The OOF results are development and selection evidence, not an untouched final test set.

The validation design preserves temporal ordering and avoids using future observations to train earlier validation periods.

## 3. Worth-It Gate

| Candidate           |     Mean AP |     Fold SD |  Mean Brier |    Mean ECE | Lift vs Single | Gate      |
| ------------------- | ----------: | ----------: | ----------: | ----------: | -------------: | --------- |
| LightGBM            |     0.34549 |     0.04348 |     0.06608 |     0.02311 |       -0.04617 | FAIL      |
| XGBoost             |     0.35263 |     0.02904 |     0.06566 |     0.02276 |       -0.03903 | FAIL      |
| Logistic Regression | **0.39166** | **0.02981** | **0.06327** | **0.01882** |        0.00000 | Reference |
| Equal Ensemble      |     0.37170 |     0.03258 |     0.06435 |     0.02038 |       -0.01996 | FAIL      |
| Weighted Ensemble   |     0.38942 |     0.02906 |     0.06332 |     0.01772 |       -0.00224 | FAIL      |
| Stack Ensemble      |     0.38314 |     0.02949 |     0.06603 |     0.03106 |       -0.00852 | FAIL      |

**Decision:** KEEP SINGLE MODEL → Logistic Regression.

No ensemble demonstrated sufficient evidence to justify additional complexity.

## 4. Cost-Sensitive Policy

The policy follows the course decision rule:

* FN cost = **10**
* FP cost = **1**
* Validation-period capacity constraint = **≤12%**

Selected threshold:

**0.168922**

Result on the OOF policy-development population:

| Metric                       |       Value |
| ---------------------------- | ----------: |
| TP                           |          84 |
| FP                           |         161 |
| FN                           |          95 |
| TN                           |       1,815 |
| Flagged                      |         245 |
| Flag fraction                |     11.369% |
| Recall                       |     0.46927 |
| Precision                    |     0.08148 |
| FPR                          |     0.88121 |
| Accuracy                     |     0.34286 |
| Loss units                   |     1,111.0 |
| Loss units / 10,000          | 5,155.45244 |
| Capacity feasible            |        True |
| Maximum period flag fraction |     11.749% |

The threshold satisfies the stated 12% validation-period capacity constraint.

## 5. Regional Audit

Regional results are reported descriptively only. They are **not** presented as a fairness certification or legal/compliance assessment.

| Region  | Rows | Negatives | Positives | Flagged | FP | TP |    FPR | Recall |
| ------- | ---: | --------: | --------: | ------: | -: | -: | -----: | -----: |
| Central |  537 |       489 |        48 |      63 | 39 | 24 | 0.0798 | 0.5000 |
| Western |  533 |       486 |        47 |      71 | 50 | 21 | 0.1029 | 0.4468 |
| Eastern |  542 |       507 |        35 |      48 | 32 | 16 | 0.0631 | 0.4571 |
| Other   |  543 |       494 |        49 |      63 | 40 | 23 | 0.0810 | 0.4694 |

These comparisons are descriptive and should not be interpreted as a fairness certificate.

## 6. Calibration

Calibration was assessed using the designated calibration rows.

Calibration-fit diagnostics:

* Calibration rows: **836**
* Positive rows: **78**
* Raw ROC-AUC: **0.789037**
* Raw Average Precision: **0.789037**
* Raw Brier: **0.287803**
* Raw log loss: **0.076473**
* Raw ECE: **0.266489**

After sigmoid calibration:

* ROC-AUC: **0.789037**
* Average Precision: **0.789037**
* Brier: **0.287803**
* Log loss: **0.078058**
* ECE: **0.277296**

These diagnostics do **not** demonstrate an improvement from calibration. ECE and log loss worsened slightly while Brier remained unchanged.

Therefore, the project does not claim that calibration universally improved model quality.

## 7. Challenge Inference

Final inference was executed on **2,500 challenge rows**.

Capacity:

* Challenge capacity: **300**
* Threshold-eligible rows: **330**
* Final flagged rows: **300**
* Removed by capacity: **30**
* Boundary score: **0.129908**
* Within capacity: **True**

Batch policy:

1. Generate probabilities first.
2. Apply the frozen threshold.
3. If the threshold produces more candidates than capacity, rank candidates by probability descending.
4. Retain the allowed capacity.
5. Apply the documented tie policy at the boundary.

Challenge labels were unavailable.

Therefore, `decision = 1` represents a **simulated review flag**, not an approval or rejection decision.

## 8. Limitations

This project is an educational and experimental implementation using synthetic course data.

Important limitations:

* OOF results are model-development and selection evidence, not an untouched final test set.
* Validation covers three forward periods and does not constitute a statistical confidence interval.
* Calibration diagnostics are limited to the designated calibration rows.
* The final Day 5 Logistic Regression model should not inherit Day 4 SHAP explanations automatically.
* Day 4 interpretability outputs relate to the Day 4 model and should not be represented as explanations of the final Day 5 model.
* Regional comparisons are descriptive and are not a fairness certification.
* Challenge labels are unavailable.
* The challenge decision is a simulated review flag.
* Thresholds and policy decisions should be reconsidered when new development data becomes available.
* Performance, calibration, prevalence, capacity, drift, false-positive and false-negative rates should be monitored before any real-world use.

## 9. Intended Use

Tamweel Lite is intended for **educational and experimental machine-learning workflow demonstration**.

It demonstrates:

* temporal validation,
* out-of-fold model comparison,
* cost-sensitive threshold selection,
* capacity-aware review policy,
* calibration diagnostics,
* ensemble evaluation,
* reproducible final inference.

It is **not intended for real-world credit approval, rejection, eligibility, legal, regulatory, or financial decision-making**.

## 10. Reproducibility

The final assessed repository should identify:

* the exact project version,
* environment and package versions,
* random seed,
* model-selection evidence,
* final policy,
* provenance,
* generated artifacts,
* and the exact Git commit/tag used for assessment.

The final repository version should be reproduced from the recorded project commit rather than from manually edited outputs.

## 11. Evidence Integrity

Example/recovery artifacts, where included in the repository, are learning aids and are explicitly distinguished from personal execution evidence.

They must not be presented as evidence that the learner personally generated those outputs during the assessed execution.

No challenge labels, private data, credentials, grades, receipts, or other prohibited sensitive material are included as project evidence.
