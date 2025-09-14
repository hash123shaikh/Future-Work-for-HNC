# 🔮 Future Work for HNC Project

This repository tracks post-conference tasks after submitting to AIHC 2025 and NIT Calicut. It centralizes coding cleanups, hybrid pipelines, dataset updates, and cohort audit work.

---

## 📌 Objectives

- Modularize the codebase for clarity and reuse.
- Implement PSO-only and Hybrid (PSO + Bootstrap LASSO) workflows.
- Keep radiomics and clinical datasets current and correctly linked.
- Audit eligibility from 460 candidates to the final 163-patient cohort.
- Implement and compare multiple feature selection techniques across data modalities:
  - Radiomics only
  - Clinical only
  - Radiomics + Clinical
  - Hybrid selection workflows

---

## ✅ To-Do List

### 1) Coding and Pipeline Integration
- [ ] Refactor into reusable modules:
  - `imports.py`
  - `data_io.py` (load and preprocess)
  - `grids.py` (hyperparameter grids)
  - `fs_bootstrap_lasso.py`
  - `eval.py` (metrics, plots, reporting)
  - `ci_bootstrap.py` (AUC confidence intervals)
- [ ] `__main__` pipeline with Hybrid (Bootstrap LASSO preselect then PSO)
- [ ] `__main__` pipeline with PSO only
- [ ] Config-driven runs (`configs/*.yaml`) for modality and feature selection choices
- [ ] Logging plus run metadata capture (random seed, versions, hashes)

### 2) Data Preparation and Updates
- [ ] Extract radiomics features for new patients up to 2 Sep 2025  
      Output: `HNC-Prospective-Radiomics-305.csv`
- [ ] Update clinical file for label linkage (LRR vs Non-LRR)  
      File: `proceed_radiomics_166.csv`
- [ ] Fix key columns used for Orthanc pulls  
      File: `combined_sheets_390.csv`  
      Fields: `patient id`, `Name of the primary gtv`

### 3) Cohort Eligibility Clarification
- [ ] Review `HNC-CMC-Prospective-460-NoDuplicates.csv`
- [ ] Document all filters and exclusion reasons that reduce 460 to 163
- [ ] Produce a transparent flow summary and a study diagram

---

## 🧪 Feature Selection Plan

You will run the same feature selection families across three modality settings and compare:

**Modalities**
- [ ] Radiomics only
- [ ] Clinical only
- [ ] Radiomics + Clinical

**Feature Selection Families**
- **Filter**
  - [ ] SelectKBest (f_classif)
  - [ ] Mutual Information
- **Embedded**
  - [ ] LASSO (Logistic with L1)
  - [ ] Elastic Net
  - [ ] Tree-based importance (Random Forest)
  - [ ] Gradient boosted importance (XGBoost)
  - [ ] Stability selection for sparse linear models
- **Wrapper and Metaheuristics**
  - [ ] RFE (with Logistic or SVC)
  - [ ] PSO
  - [ ] GA
  - [ ] WOA
  - [ ] GWO
  - [ ] SA
- **Hybrid**
  - [ ] Bootstrap LASSO preselect then PSO
  - [ ] LASSO or Elastic Net preselect then GA or WOA or GWO
  - [ ] Consensus voting across folds with thresholding (for stability)

> Tip: use a standard interface `fit_transform(X, y)` for all selectors so pipelines are interchangeable.

---

## 🧩 Hybrid Strategies

- **Bootstrap LASSO preselection**
  - Run B bootstraps with LASSO
  - Keep features that exceed frequency threshold `tau` (for example 0.6 to 0.8)
  - Feed retained subset to PSO or another wrapper
- **Stability selection**
  - Subsample repeatedly
  - Aggregate selection probabilities per feature
  - Select by probability cutoff
- **Cross-fold consensus**
  - Track selection occurrences across 5 folds
  - Keep features selected in k of 5 folds (for example k equals 3 or 4)

---

## 🔧 Models and Grids

Implement grid or sensible defaults per classifier. Keep all grids in `grids.py`.

- Logistic Regression: `C`, `penalty` in {l1, l2}, solver `liblinear` for l1
- SVC: `C`, kernels in {linear, rbf}, `probability=True`
- Random Forest: `n_estimators`, `max_depth`, `min_samples_leaf`
- XGBoost: `n_estimators`, `max_depth`, `learning_rate`, `subsample`
- GaussianNB: no grid
- Optionally add LightGBM or CatBoost with caution

---

## 📏 Evaluation Protocol

- **Split**: fixed 80 to 20 train to test with stratification
- **Within-train**: 5-fold stratified cross-validation
- **Report**
  - Cross-validation mean and standard deviation for AUC and Accuracy
  - Final test AUC with bootstrap 95 percent CI
  - Final test Accuracy, Precision, Recall, F1
  - Calibration curve and Brier score if time permits
- **Class imbalance**
  - Use stratification
  - Consider class weights or threshold tuning
- **Reproducibility**
  - Set seeds for NumPy and all learners
  - Save config and environment info with each run
  - Persist feature lists per run

---

## 📂 Deliverables

- [ ] Modularized codebase with clear package layout
- [ ] Two runnable pipelines
  - Hybrid (Bootstrap LASSO then PSO)
  - PSO only
- [ ] Updated CSVs
  - `HNC-Prospective-Radiomics-305.csv`
  - `proceed_radiomics_166.csv`
  - `combined_sheets_390.csv`
- [ ] Eligibility audit note: filters that reduce 460 to 163
- [ ] Feature selection comparison report across modalities

---

## 🗂 Suggested Repository Structure

