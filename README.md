Tamweel Lite — Advanced Machine Learning Methods

An educational machine learning project developed as part of the SDAIA Academy — Advanced Machine Learning Methods training programme.

Course: SDA-DSC-211 — Advanced Machine Learning Methods | أساليب تعلم الآلة المتقدمة

Project Purpose

Tamweel Lite demonstrates an end-to-end machine learning workflow for credit-risk prioritization and simulated review screening using synthetic course data.

The project focuses on:

classification model comparison
time-aware validation
out-of-fold (OOF) probability generation
cost-sensitive threshold selection
capacity-constrained decision policies
calibration diagnostics
model stability
interpretability
ensemble evaluation
reproducible final inference

The project is educational and experimental. It does not represent a real lending system or a real credit decision.

Problem

The project addresses a binary classification problem in which applications are assigned estimated risk probabilities and then prioritized for a simulated review process.

The workflow evaluates models beyond ROC-AUC by considering:

Average Precision
time-aware validation
out-of-fold probabilities
threshold selection
simulated decision loss
review-capacity constraints
calibration diagnostics
model stability
interpretability
operational complexity

The educational decision policy uses:

10 × FN + 1 × FP

with a simulated review-capacity ceiling of 12%.

The cost values are educational units only. They do not represent Saudi Riyal losses, expected credit loss, or real financial costs.

Project Workflow

The course work is maintained in a consolidated learner notebook covering Day 1 through Day 5, following the instructor's delivery instruction to place the course work in one notebook with clear day headings.

Day 1 — Baseline Boosting
Data preparation
Baseline classification modelling
Initial model comparison
Day 2 — Validation & Tuning
Time-aware validation
Out-of-fold predictions
Model validation and tuning
Day 3 — Imbalance, OOF Probabilities, Cost-Sensitive Decisions
Class imbalance
OOF probability analysis
Cost-sensitive threshold selection
Capacity-aware decision rules
Day 4 — Explainability, Calibration, and Stability
Model explainability
SHAP analysis
Calibration diagnostics
Stability and diagnostic analysis
Day 5 — Ensemble, Final Model, and Challenge Inference
Ensemble comparison
Worth-It Gate
Final model selection
Frozen threshold application
Capacity-constrained simulated review screening
Challenge inference
Data

The project uses synthetic course data only.

No confidential employer data, third-party personal data, credentials, or real customer lending records are used.

The challenge dataset is intentionally unlabeled. Challenge labels are not used or inferred.

The workflow maintains separate roles for model development, calibration, policy selection, and challenge inference to reduce leakage and preserve the intended evaluation design.

Models

The project evaluates:

Logistic Regression
XGBoost
LightGBM

Ensemble alternatives were also evaluated using:

Equal-weight ensemble
Weighted ensemble
Stacking
Final Model Decision

The final decision was:

KEEP SINGLE → Logistic Regression

Logistic Regression achieved the highest mean Average Precision among the single-model candidates:

Candidate	Mean Average Precision
Logistic Regression	0.39166
Weighted Ensemble	0.38942
Stacking	0.38314
Equal-weight Ensemble	0.37170
XGBoost	0.35263
LightGBM	0.34549

None of the ensemble alternatives passed the project's Worth-It Gate. Therefore, the additional complexity of an ensemble was not justified by the observed validation evidence.

Choosing a single model was an evidence-based decision rather than an assumption that ensembles are always better.

Validation

Model comparison used 2,155 OOF rows across three forward validation periods.

The OOF predictions were used for model comparison and decision-policy development.

OOF results are not treated as an untouched final test set. The observed fold variation does not represent a confidence interval, and validation performance cannot guarantee future production performance.

The validation design uses forward temporal separation to reduce look-ahead leakage.

Decision Policy

The educational decision policy uses:

10 × FN + 1 × FP

with a maximum review-capacity ceiling of 12%.

The selected raw threshold was:

0.168922

The threshold was selected using OOF development evidence rather than the unlabeled challenge batch.

Challenge Screening

The final model was applied to 2,500 challenge applications.

Measure	Result
Challenge rows	2,500
Initially eligible by threshold	330
Final review flags after capacity policy	300
Final flagged rate	12%
Removed by capacity policy	30

The batch policy applies the probability threshold first and then applies the capacity constraint once.

A final decision = 1 means that an application was included in the simulated review list. It does not mean loan approval, loan rejection, or a real-world credit decision.

Because challenge labels are unavailable, the challenge set cannot be used to calculate:

Average Precision
decision loss
false-positive rate
recall
other label-dependent performance metrics

No challenge performance metrics have been fabricated.

Calibration Fit Diagnostics

Calibration was fitted using a dedicated calibration sample of 836 rows from July–September 2024.

The following are calibration-fit diagnostics, not independent final evaluation results:

Metric	Before	After
ROC-AUC	0.789037	0.789037
Average Precision	0.789037	0.789037
ECE	0.266489	0.277296
Log Loss	0.076473	0.078058
Brier Score	0.287803	0.287803

In this calibration sample:

ROC-AUC remained unchanged.
Average Precision remained unchanged.
Brier Score remained unchanged.
ECE increased from 0.266489 to 0.277296.
Log Loss increased from 0.076473 to 0.078058.

Therefore, the project does not claim that calibration improved model performance.

Calibration is treated as a limited diagnostic, and these fit diagnostics should not be interpreted as independent evidence of future calibration quality.

Interpretability Scope

Model interpretation is intended to explain model behaviour, not establish causal relationships.

SHAP values describe contributions to the model output in the units produced by the interpretation method. They should not be interpreted directly as percentage-point changes in probability.

The interpretation workflow does not establish:

causality
fairness
legality or regulatory compliance
the effect of protected characteristics
why a real customer would default
Final-model interpretation limitation

The SHAP analysis generated during the earlier project stage belongs to an earlier model stage and should not be treated as evidence explaining the final Day 5 Logistic Regression model unless SHAP is regenerated specifically for that final model.

This distinction is intentionally documented to avoid transferring explanation evidence between different model versions.

Intended Use

Tamweel Lite is an educational and experimental machine learning project.

Its purpose is to demonstrate:

model comparison
honest validation
OOF probability generation
cost-sensitive decision making
capacity-aware policies
calibration diagnostics
ensemble evaluation
reproducible inference

The final decision flag represents inclusion in a simulated review list only.

It does not represent:

loan approval
loan rejection
a real credit decision
a guarantee of customer default
a guarantee of model safety
a fairness certification
a regulatory or legal assessment

The project uses synthetic data and does not support conclusions about real customers or real lending performance.

Environment

The assessed learning path is designed to run on a free Google Colab CPU environment.

Runtime
Google Colab
Free CPU runtime
Python 3.13.16
FAST_MODE=True
seed: 211
n_jobs=2

GPU, TPU, paid services, API keys, and external paid inference services are not required.

Key package versions

The executed environment recorded the following versions:

Package	Version
NumPy	2.1.3
pandas	2.2.3
SciPy	1.16.3
scikit-learn	1.6.1
matplotlib	3.10.0
XGBoost	3.4.1
LightGBM	4.6.0
SHAP	0.52.0
numba	0.61.2
Optuna	4.5.0

The final repository should preserve the recorded environment and provenance artifacts generated by the project.

Reproducibility

The project uses fixed seeds and records environment and execution information required to reproduce the assessed workflow.

The final repository should preserve:

executed learner notebook
environment information
package versions
random seed
model and policy parameters
data-role information
validation evidence
generated metrics
provenance information
manifest
final bundle
final Git commit SHA
final Git tag

The assessed version must be identifiable by its exact repository commit.

The final repository should be rerunnable from a clean free-CPU environment without relying on hidden local files, paid services, private credentials, or challenge labels.

How to Run
Google Colab
Open the consolidated learner notebook in Google Colab.
Confirm the free CPU runtime and course-approved configuration.
Run the notebook sequentially from Day 1 through Day 5.
Preserve the executed outputs and learner reasoning.
Review the generated validation, decision, calibration, ensemble, and challenge-inference outputs.
Run the final validation and submission checks.
Preserve the final generated artifacts and provenance files.
Use the exact final repository version for the presentation and submission.

The consolidated notebook uses explicit Day 1–Day 5 headings so that the complete learning path and evidence can be followed within one executable notebook.

Outputs

The final repository is intended to provide clear access to the project evidence and required deliverables, including:

consolidated executed learner notebook
technical documentation
Decision Card
Interpretability Report
Ensemble Decision
Model Card
evaluation metrics
model artifacts
provenance and manifest files
submission.csv
metrics.json
final presentation PDF
final repository bundle

The repository structure and links should be kept consistent with the official learner template and the final assessed version.

Technical Decision Summary
Decision	Final result
Validation design	Time-aware forward validation
OOF rows	2,155
Final model	Logistic Regression
Ensemble decision	KEEP SINGLE
Worth-It Gate	No ensemble passed
Raw threshold	0.168922
Capacity ceiling	12%
Challenge rows	2,500
Final challenge review flags	300
Challenge labels	Unavailable
Challenge performance metrics	Not computed
Calibration conclusion	No improvement demonstrated in calibration-fit diagnostics
Limitations

Key limitations include:

Validation evidence is based on three forward periods.
OOF results are used for development and model selection rather than as an untouched final test.
Fold-level variation does not constitute a confidence interval.
The challenge dataset is unlabeled.
Calibration diagnostics are based on a limited calibration sample.
Calibration-fit diagnostics did not demonstrate improvement in ECE or Log Loss.
The simulated cost policy is educational and does not represent Saudi Riyal losses or expected credit loss.
The capacity policy is a simulated operational constraint.
Regional analysis is descriptive and does not constitute a fairness certification.
Model explanations describe model behaviour rather than causal relationships.
Earlier-stage SHAP evidence should not be transferred to the final model without regeneration.
Future production performance cannot be guaranteed from the current validation evidence.
The synthetic dataset does not support conclusions about real borrowers, customers, or lending outcomes.
Assessment Integrity

This repository is intended to preserve an auditable record of the learner's executed project.

No challenge labels, private credentials, confidential employer information, or unnecessary personal data are included.

Educational/example outputs, if referenced, are treated as learning aids and are not presented as personal execution evidence.

Metrics and decisions reported in this README are based on the executed project outputs and are not intended to be fabricated, backfilled, or replaced with peer evidence.

References and Disclosure
Course and technical references

The project was developed using the course materials and the standard open-source Python ecosystem used by the training environment.

The primary programme reference is:

SDAIA Academy — Advanced Machine Learning Methods
Course Code: SDA-DSC-211

SDAIA Academy

This repository is a learner repository and is not an official SDAIA repository.

SDAIA Academy GitHub:

https://github.com/SDAIAAcademy

Assistance and reused material

The learner's project work was developed and executed within the course environment.

External libraries are used according to their respective licenses and documentation.

AI-assisted support was used during the learning and documentation process for explanation, interpretation, troubleshooting, and drafting of documentation. Final project decisions, execution evidence, metrics, and repository contents are based on the learner's executed project.

No peer execution evidence, fabricated metrics, fabricated screenshots, fabricated commits, or challenge labels are used as personal project evidence.

Course Information

Training Programme: SDAIA Academy — Advanced Machine Learning Methods
Course Code: SDA-DSC-211
Project: Tamweel Lite

SDAIA Academy GitHub:
https://github.com/SDAIAAcademy

Repository Status

The final assessed version should be identified by:

final repository URL
final commit
final Git tag
full commit SHA
generated manifest
final project bundle

The repository should remain available for review after submission and the assessed tag/commit should not be rewritten or force-pushed.
