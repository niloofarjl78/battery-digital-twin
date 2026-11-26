# An Explainable Digital Twin Prototype for Lithium-Ion Battery Health Monitoring

## 1. Project Overview

This project develops a data-driven Digital Twin prototype for lithium-ion battery health monitoring using experimental ageing data from NASA's Battery Aging Dataset.

The framework integrates operational battery measurements and electrochemical impedance indicators to investigate battery degradation and estimate State of Health (SoH).

The prototype combines data integration, feature engineering, machine learning, hybrid prediction, and cross-battery validation.

## 2. Project Objectives

The main objectives are to:

- Integrate operational discharge and electrochemical impedance measurements.
- Extract electrical, thermal, and impedance-related features.
- Develop data-driven models for battery State-of-Health estimation.
- Evaluate the contribution of impedance measurements to predictive performance.
- Implement a hybrid estimation strategy to handle incomplete impedance observations.
- Investigate voltage relaxation and apparent resistance as degradation-related indicators.
- Assess model generalisation across different battery cells.

## 3. Dataset

**Source:** NASA Ames Prognostics Center of Excellence (PCoE) Battery Data Set.

The project analyses four lithium-ion battery cells:

- B0005
- B0006
- B0007
- B0018

The dataset contains experimental charge, discharge, and electrochemical impedance measurements collected during battery ageing tests.

A total of 636 discharge observations were analysed, including 419 observations with matched impedance measurements.

**Dataset source:**  
[NASA PCoE Battery Data Set](https://www.nasa.gov/intelligent-systems-division/discovery-and-systems-health/pcoe/pcoe-data-set-repository/)

## 4. Methodology

### 4.1 Data Integration and Feature Engineering

Operational measurements are processed to extract relevant battery characteristics, including:

- Voltage
- Current
- Temperature
- Discharge capacity
- Electrochemical impedance indicators

The impedance-enhanced feature set additionally includes:

- Electrolyte resistance ($R_e$)
- Charge-transfer resistance ($R_{ct}$)

Discharge duration is excluded from the predictive feature set because it is directly involved in the calculation of discharge capacity and could introduce target leakage.

### 4.2 State-of-Health Estimation

Battery State of Health is calculated relative to the initial measured discharge capacity:

$$
SoH_i = \frac{Q_i}{Q_{\mathrm{initial}}} \times 100
$$

where:

- $Q_i$ represents the discharge capacity at cycle $i$.
- $Q_{\mathrm{initial}}$ represents the initial measured discharge capacity.

### 4.3 Machine Learning Models

Ridge Regression is used to estimate battery SoH.

**Baseline model**

The baseline model uses operational measurements:

$$
\widehat{SoH}_A = f(X_{\mathrm{operational}})
$$

**Impedance-enhanced model**

The impedance-enhanced model incorporates impedance-derived features:

$$
\widehat{SoH}_B =
f(X_{\mathrm{operational}}, R_e, R_{ct})
$$

Feature standardisation is performed using `StandardScaler`, and Ridge Regression is applied with a regularisation parameter of $\alpha = 1$.

### 4.4 Hybrid Prediction Strategy

A hybrid estimation strategy combines the baseline and impedance-enhanced models to accommodate incomplete impedance observations.

$$
\widehat{SoH}_{\mathrm{hybrid}} =
\begin{cases}
\widehat{SoH}_B, & \text{if impedance data are available} \\
\widehat{SoH}_A, & \text{otherwise}
\end{cases}
$$

This approach allows the framework to generate predictions across the complete discharge dataset while using impedance information whenever available.

### 4.5 Cross-Battery Validation

Model generalisation is evaluated using Leave-One-Battery-Out (LOBO) cross-validation.

In each validation iteration:

1. Three battery cells are used for model training.
2. The remaining battery cell is held out for testing.
3. The process is repeated until each battery has served as the test cell.

This strategy evaluates the ability of the models to generalise to battery cells not included in the corresponding training set.

### 4.6 Additional Degradation Analysis

Two additional analyses investigate potential degradation-related indicators:

- **Voltage relaxation analysis:** Examines relaxation characteristics and their relationship with battery SoH.
- **Apparent resistance analysis:** Investigates a resistance-related indicator derived from relaxation amplitude and discharge current.

These indicators are evaluated separately from the main predictive model.

## 5. Results

### 5.1 Hybrid SoH Estimation

The hybrid estimation strategy achieved a **13.68% reduction in weighted Mean Absolute Error (MAE)** compared with the baseline model across 636 discharge observations.

| Evaluation | Weighted MAE |
|---|---:|
| Baseline model | 2.9799 |
| Hybrid model | 2.5723 |

The improvement reflects the benefit of incorporating impedance measurements when available while retaining baseline predictions for observations without impedance data.

### 5.2 Impedance Contribution

The impedance-enhanced model was also evaluated against the baseline using the 419 matched impedance observations.

The results indicate that impedance features can improve predictive performance, although their contribution varies across battery cells.

### 5.3 Additional Diagnostic Findings

Voltage relaxation and apparent resistance were investigated as potential degradation indicators.

Although these indicators showed relationships with battery SoH, the additional relaxation and apparent resistance features did not consistently improve predictive performance in the evaluated cross-validation experiments.

They are therefore retained as diagnostic indicators rather than selected predictive features.

## 6. Repository Structure

```text
battery-digital-twin/
│
├── data/
│   └── README.md
│
├── notebooks/
│   ├── 01_Battery_Digital_Twin_Data_Exploration_and_Modelling.ipynb
│   ├── 02_Battery_Digital_Twin_Architecture.ipynb
│   └── README.md
│
├── results/
│   ├── README.md
│   ├── hybrid_soh_residuals.png
│   ├── hybrid_soh_validation.png
│   ├── impedance_contribution_analysis.csv
│   ├── lobo_relaxation_overall_results.csv
│   ├── model_A_vs_hybrid.csv
│   ├── modelling_summary.md
│   ├── residual_statistics.csv
│   ├── revised_resistance_model_summary.csv
│   └── revised_resistance_vs_soh.png
│
└── README.md
```

## 7. Technologies and Methods

- Python
- Jupyter Notebook
- NumPy and Pandas
- Scikit-learn
- Matplotlib and Seaborn
- Ridge Regression
- Feature Engineering
- Electrochemical Impedance Analysis
- Leave-One-Battery-Out Cross-Validation

## 8. Limitations and Future Development

The current implementation is an offline, data-driven Digital Twin prototype based on historical experimental measurements.

It does not yet include a live connection to physical battery systems or continuous real-time state synchronisation.

Potential future developments include:

- Integration with real-time battery management systems.
- Dynamic digital state updating.
- Physics-informed degradation modelling.
- Uncertainty quantification.
- Deployment for continuous battery health monitoring.

## 9. Project Notebooks

**Notebook 01 — Data Exploration and Modelling**

Covers data processing, feature engineering, SoH estimation, model evaluation, and degradation analysis.

**Notebook 02 — Digital Twin Architecture**

Presents the conceptual Digital Twin architecture, battery state representation, data integration, and modelling workflow.

## 10. Author

**Niloofar Joulaei**

Research interests: Digital Twins, Industrial AI, Data Analytics, Predictive Modelling, and Smart Systems.
