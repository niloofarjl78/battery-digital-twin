# Explainable Battery Digital Twin

An explainable Digital Twin prototype for lithium-ion battery health monitoring, combining NASA battery ageing data, interpretable health indicators, and machine-learning-based State-of-Health (SoH) estimation.

## Project Overview

This project investigates how operational measurements and interpretable degradation indicators can support the development of a Digital Twin for lithium-ion battery health monitoring.

Using the NASA Battery Ageing Dataset, the project develops a data-processing and modelling workflow to estimate battery State of Health (SoH), investigate resistance-related degradation indicators, and evaluate model generalisation across different battery cells.

The prototype represents battery condition through an updated digital state containing operational measurements, capacity-based health indicators, impedance measurements, and estimated SoH.

## Research Question

**How can interpretable electrical, thermal, and impedance-based health indicators be integrated with machine learning to improve the accuracy, interpretability, and cross-battery generalisability of State-of-Health estimation for lithium-ion batteries?**

## Research Objectives

- Extract and process battery charge, discharge, and impedance measurements.
- Derive capacity-based State-of-Health indicators.
- Investigate the relationship between electrical measurements, impedance parameters, and battery degradation.
- Develop and evaluate machine-learning models for SoH estimation.
- Implement a structured Digital Twin state representation.
- Assess model generalisation using leave-one-battery-out validation.

## Digital Twin Architecture

The proposed prototype is organised into interconnected layers:

**Physical Data Layer**

- Battery charge and discharge measurements.
- Voltage, current, and temperature observations.
- Capacity and impedance measurements.

**Data Processing Layer**

- MATLAB data extraction.
- Timestamp processing and data integration.
- Feature engineering and impedance-to-discharge matching.
- Data quality checks and missing-value handling.

**Digital State Layer**

- Battery identification and observation tracking.
- Capacity and measured SoH.
- Electrical and thermal indicators.
- Impedance measurements.
- Estimated SoH.

**Health Estimation and Diagnostic Layer**

- Ridge regression for SoH estimation.
- Cross-battery model validation.
- Prediction error and residual analysis.
- Investigation of interpretable degradation indicators.

## Dataset

The project uses four lithium-ion battery cells from the NASA Ames Prognostics Center of Excellence (PCoE) Battery Data Set:

- B0005
- B0006
- B0007
- B0018

The dataset includes charge, discharge, and impedance measurements collected during battery ageing experiments.

**Dataset source:** [NASA PCoE Battery Data Set](https://www.nasa.gov/intelligent-systems-division/discovery-and-systems-health/pcoe/pcoe-data-set-repository/)

**Dataset citation:**

Saha, B. and Goebel, K. (2007). *Battery Data Set*. NASA Prognostics Data Repository, NASA Ames Research Center, Moffett Field, CA.

The original MATLAB files are not included in this repository. Refer to [`data/README.md`](data/README.md) for download and setup instructions.

## Methodology

### Data Extraction and Feature Engineering

Discharge measurements are processed to derive:

- Discharge capacity.
- Mean, minimum, and maximum voltage.
- Mean current.
- Mean and maximum temperature.
- Temperature rise.
- Discharge duration.
- Capacity-based State of Health.

Discharge duration is retained as a descriptive variable but excluded from the SoH prediction models to avoid potential target leakage, since discharge capacity is derived from current and discharge time.

### State-of-Health Calculation

The State of Health (SoH) is calculated relative to the first measured discharge capacity of each battery:

$$
\mathrm{SoH}_i = \frac{Q_i}{Q_{\mathrm{initial}}} \times 100
$$

where:

- \(Q_i\) is the discharge capacity measured at observation \(i\) (Ah).
- \(Q_{\mathrm{initial}}\) is the first measured discharge capacity of the same battery (Ah).

Impedance measurements, including electrolyte resistance (Re) and charge-transfer resistance (Rct), are matched to discharge observations using timestamp-based matching. Missing impedance measurements are preserved rather than replaced with artificial values.

### State-of-Health Estimation

Ridge regression models are developed using electrical and thermal features, with additional experiments incorporating impedance-related indicators.

Model performance is evaluated using:

- Mean Absolute Error (MAE).
- Root Mean Squared Error (RMSE).
- Coefficient of Determination (R²).

### Cross-Battery Validation

A leave-one-battery-out (LOBO) validation strategy is used. In each experiment, three battery cells are used for training and the remaining cell is held out for testing.

This evaluates the ability of the model to generalise to a battery not represented in its training data.

### Interpretable Health Indicators

The project investigates the relationship between SoH and resistance-related indicators, including:

- Electrolyte resistance (Re).
- Charge-transfer resistance (Rct).
- An apparent resistance proxy derived from post-discharge voltage relaxation.

The apparent resistance is estimated using the fitted relaxation voltage amplitude and the current immediately before discharge termination.

These indicators are treated as diagnostic proxies rather than independently validated electrochemical parameters.

## Key Results

### Baseline Model: Leave-One-Battery-Out Validation

The initial Ridge regression model, using electrical and thermal features, achieved the following results:

| Held-out battery | MAE (%) | RMSE (%) | R² |
|---|---:|---:|---:|
| B0005 | 1.7923 | 2.5010 | 0.9402 |
| B0006 | 3.4296 | 4.2320 | 0.8825 |
| B0007 | 4.2123 | 4.9917 | 0.6540 |
| B0018 | 1.5000 | 1.7369 | 0.9564 |

The results indicate differences in predictive performance across battery cells, highlighting the challenge of cross-battery generalisation.

### Contribution of Impedance Measurements

Further experiments investigated the contribution of impedance features using the same matched observations for both models.

The impedance-augmented model reduced the weighted MAE from **2.6949% to 2.0762%**, corresponding to a 22.96% reduction in this matched-observation comparison.

However, the improvement varied across battery cells. Impedance features improved performance for some cells but reduced it for others, indicating that their predictive contribution is battery-dependent.

### Hybrid SoH Estimation

A hybrid estimation strategy was also evaluated. It uses the impedance-augmented model when impedance measurements are available and the baseline model as a fallback when they are missing.

Across all test observations, the hybrid approach achieved an overall MAE of **2.5723%**, compared with **2.9799%** for the baseline model evaluated on the same test observations. This corresponds to a **13.68% reduction in MAE**.

However, performance varied across battery cells, reflecting differences in impedance availability and model generalisation.

### Resistance-Related Diagnostic Analysis

The analysis identified strong associations between resistance-related indicators and battery SoH in several cells.

However, these relationships do not necessarily translate into improved cross-battery prediction. Experiments incorporating the apparent resistance proxy did not improve the overall weighted MAE of the baseline model.

This distinction highlights the difference between an indicator's diagnostic relationship with battery degradation and its usefulness as a predictive feature.

## Repository Structure

The repository is organised into three main directories:

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
│   ├── modelling_summary.md
│   ├── *.csv
│   └── *.png
│
├── .gitignore
└── README.md
```

- `data/`: Instructions for obtaining and preparing the original NASA battery dataset.
- `notebooks/`: Jupyter notebooks for data exploration, feature engineering, SoH estimation, model validation, and Digital Twin architecture development.
- `results/`: Exported datasets, model evaluation metrics, diagnostic analyses, and visualisations.
- `.gitignore`: Excludes raw data files and temporary development files from version control.

## Digital Twin Architecture

The proposed architecture follows a data-driven Digital Twin workflow for lithium-ion battery health monitoring. It integrates battery operational measurements, impedance-derived indicators, machine learning models, and battery-state updates to estimate State of Health (SoH).

### System Workflow

The architecture consists of the following main components:

1. **Data Acquisition:** The NASA Battery Aging dataset provides charge, discharge, and electrochemical impedance measurements for four lithium-ion batteries (B0005, B0006, B0007, and B0018).

2. **Feature Extraction:** Discharge-related features are extracted, including voltage, current, temperature, discharge capacity, and calculated SoH. Impedance measurements, including electrolyte resistance (Re) and charge-transfer resistance (Rct), are processed separately.

3. **Data Integration:** Discharge and impedance observations are integrated through timestamp-based matching. Impedance measurements are associated with discharge observations when they fall within the specified one-hour matching window. Missing impedance measurements are retained rather than discarded.

4. **Health Estimation:** Two Ridge Regression models are considered: a baseline model using operational features and an impedance-enhanced model incorporating impedance-derived indicators. A hybrid prediction strategy uses the impedance-enhanced model when impedance data are available and falls back to the baseline model otherwise.

5. **Model Validation:** The models are evaluated using Leave-One-Battery-Out (LOBO) validation, in which three batteries are used for training and the remaining battery is held out for testing. This evaluates the ability to generalise to an unseen battery.

6. **Digital Twin State Update:** Estimated SoH and available measurements are incorporated into a battery-state representation, enabling the monitoring of battery health across successive observations.

### Architecture Diagram


flowchart TD
    A["NASA Battery Aging Dataset"]
    B["Data Extraction"]
    C["Discharge Features"]
    D["Impedance Features"]
    E["Timestamp-Based Data Integration"]
    F["Combined Battery Dataset"]
    G["Feature Engineering"]
    H["Baseline SoH Model"]
    I["Impedance-Enhanced SoH Model"]
    J["Hybrid Prediction Strategy"]
    K["Battery State Update"]
    L["SoH Monitoring"]
    M["LOBO Model Validation"]
    N["Performance Analysis"]

    A --> B
    B --> C
    B --> D
    C --> E
    D --> E
    E --> F
    F --> G
    G --> H
    G --> I
    H --> J
    I --> J
    J --> K
    K --> L
    J --> M
    M --> N


### Implementation Scope

The current implementation demonstrates a data-driven Digital Twin prototype. It establishes a workflow for integrating battery measurements, estimating SoH, handling incomplete impedance observations, and updating a battery-state representation.

The current prototype does not yet implement a validated electrochemical battery model or a real-time connection to a physical battery. These are potential directions for further development.

## Results

The project evaluates battery State-of-Health estimation using operational and impedance-derived features, with Leave-One-Battery-Out validation across four battery cells.

The experiments investigate:
- Baseline versus impedance-enhanced SoH estimation.
- A hybrid prediction strategy for handling missing impedance measurements.
- The contribution of relaxation-derived indicators and apparent resistance proxies.
- Model generalisation across previously unseen battery cells.

Detailed experimental results, performance comparisons, visualisations, and analysis are available in [`results/`](results/).

### Reproducibility

To run the notebooks in Google Colab:

1. Download the [NASA Battery Data Set](https://phm-datasets.s3.amazonaws.com/NASA/5.+Battery+Data+Set.zip).
2. Extract the archive and locate `B0005.mat`, `B0006.mat`, `B0007.mat`, and `B0018.mat`.
3. Open Notebook 01 in Google Colab.
4. When prompted by the notebook, upload the four MATLAB files into the Colab session.
5. Run Notebook 01 to extract and process the battery data and generate the combined dataset.
6. Open Notebook 02 and follow its setup instructions to load the generated dataset and explore the Digital Twin architecture.

The original MATLAB files are not included in this repository because of their size. See [`data/README.md`](data/README.md) for dataset information and citation details.

## Tools and Technologies

- Python
- NumPy and Pandas
- SciPy
- Matplotlib
- Scikit-learn
- Jupyter Notebook / Google Colab
- MATLAB data (`.mat`) processing

## Limitations and Future Work

The current implementation is a prototype focused on data-driven health estimation and interpretable indicators. It does not yet constitute a fully validated electrochemical Digital Twin.

The main limitations include differences in predictive performance across battery cells, incomplete impedance availability, and the absence of independently validated electrochemical model parameters.

Future development may include:

- Equivalent Circuit Model (ECM) parameter identification and validation.
- Physics-informed battery degradation modelling.
- Integration with electrochemical simulation frameworks such as PyBaMM.
- Improved temporal validation and uncertainty quantification.
- Online state updates using incoming battery measurements.
- Integration with battery management and monitoring systems.

## Author

Developed as an independent research and technical portfolio project exploring explainable Digital Twins, battery health monitoring, and predictive modelling.
