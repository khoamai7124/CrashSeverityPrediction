# Predicting Road Crash Severity — SAS Enterprise Miner

## Overview
This project uses SAS Enterprise Miner to build and compare predictive models 
that identify high-severity crashes in Victoria's road crash dataset. The goal 
is to support road-safety decision-making by flagging crash patterns associated 
with severe outcomes, helping prioritise investigation and resource allocation.

**Target variable:** `TargetSevere` (binary — severe vs. not severe)

## Dataset
- Source: Victoria road crash dataset (`Victoria_Crash_Train.csv` / `Victoria_Crash_Test.csv`)
- ~50,000 records, 28+ predictor variables covering crash circumstances, 
  road conditions, vehicle types, and persons involved
- Note: dataset contains *recorded crashes only* — no non-crash exposure data

## Approach

### 1. Data Preparation
- Converted `DCA_CODE` to character and regrouped ~130 raw contributing-factor 
  codes into interpretable crash-mechanism categories
- Imputed missing values (mean/mode) via the Replacement node
- Investigated Variable Clustering (VarClus) for dimensionality reduction — 
  found weak correlation structure (~26% variation explained at 10 clusters), 
  so proceeded with the full variable set rather than cluster representatives

### 2. Models Compared
| Model | Role |
|---|---|
| Logistic Regression | Baseline / simple reference point |
| Decision Tree | First candidate — interpretable, rule-based |
| Random Forest | Second candidate — ensemble, tested at 2 tuning configurations |

### 3. Evaluation
- Primary measure: Validation misclassification rate
- Secondary measures: Recall, Precision, F1 — prioritised because a missed 
  severe crash (false negative) is more operationally costly than a false alarm
- Cost-sensitivity check via a Decision node (assumed 3:1 cost ratio, 
  missed severe crash vs. false alarm), including a stress test at a higher ratio
- Final model comparison and scoring performed via SAS EM's Model Comparison 
  and Score nodes

## Results

| Model | Misclass. (Val) | Recall | Precision | F1 |
|---|---|---|---|---|
| Logistic Regression | 0.36 | 0.629 | 0.636 | 0.633 |
| Decision Tree | 0.38 | 0.708 | 0.601 | 0.650 |
| Random Forest | 0.36 | 0.653 | 0.630 | 0.641 |

**Chosen model: Random Forest** — matched the baseline on overall accuracy 
while achieving the best Recall/Precision balance (highest F1), and remained 
stable under cost-sensitivity testing.

## Key Findings
- Speed zone, road geometry, and vehicle mix were consistently associated with 
  crash severity across models
- All three model families converged on similar performance, suggesting the 
  predictive ceiling reflects genuine limits in the available data rather than 
  a weakness in any one modelling approach
- Findings represent **association, not causation** — see Limitations

## Limitations
- No exposure data (non-crash trips/situations) — model reflects patterns in 
  recorded crashes only, not crash likelihood relative to traffic volume
- Associations identified should not be interpreted as causal relationships
- Moderate overall accuracy (~64%) indicates a meaningful share of severity 
  is driven by factors not captured in the dataset

## Tools
- SAS Enterprise Miner (data preparation, modelling, scoring)
- SAS Base (custom code for variable transformation and diagnostics)

## Project Structure
```
├──data
├──report
├── diagrams/ # SAS EM process flow screenshots
├── output/ # Model comparison tables, ROC charts, scoring results
├── sas-code/ # Custom SAS Code node scripts (DCA_CODE_C creation, etc.)
└── README.md
```

## Author
Anh Khoa Mai — Business Analytics, La Trobe University
