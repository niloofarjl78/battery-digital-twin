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

SoH is calculated relative to the first measured discharge capacity of each battery:

The State of Health (SoH) is calculated relative to the first measured discharge capacity of each battery:

$$
\mathrm{SoH}_i = \frac{Q_i}{Q_{\mathrm{initial}}} \times 100
$$

where:
- $Q_i$ is the discharge capacity measured at observation $i$ (Ah).
- $Q_{\mathrm{initial}}$ is the first measured discharge capacity of the same battery (Ah).

Impedance measurements, including electrolyte resistance (Re) and charge-transfer resistance (Rct), are matched to discharge observations using timestamp-based matching.

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

The project investigates the relationship between SoH and resistance-related indicators, including Re and an apparent resistance proxy derived from post-discharge voltage relaxation.

These indicators are treated as diagnostic proxies rather than independently validated electrochemical parameters.

## Key Results

The initial Ridge regression model, evaluated using leave-one-battery-out validation, achieved the following results:

| Held-out battery | MAE (%) | RMSE (%) | R² |
|---|---:|---:|---:|
| B0005 | 1.7923 | 2.5010 | 0.9402 |
| B0006 | 3.4296 | 4.2320 | 0.8825 |
| B0007 | 4.2123 | 4.9917 | 0.6540 |
| B0018 | 1.5000 | 1.7369 | 0.9564 |

Further experiments investigated the contribution of impedance features using the same matched observations for both models.

The impedance-augmented model reduced the weighted MAE from 2.6949% to 2.0762% in this matched-observation comparison. However, the improvement varied across battery cells, indicating that the contribution of impedance measurements is battery-dependent.

A hybrid estimation strategy was also explored, combining the baseline model with impedance-augmented predictions when impedance measurements were available. Its overall MAE was 2.5723%, compared with 2.9799% for the baseline model evaluated on the same test observations.

These findings highlight the potential value of impedance information while also demonstrating the importance of missing-data patterns and cross-battery generalisation.

## Repository Structure

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

## Reproducibility

1. Clone or download this repository.
2. Follow the instructions in `data/README.md` to obtain the NASA battery dataset.
3. Place the four required MATLAB files in the `data/` directory.
4. Open the notebooks in Google Colab or a local Jupyter environment.
5. Run the data extraction and modelling notebook before the Digital Twin architecture notebook.

The notebooks contain the data-processing, feature engineering, modelling, and evaluation workflows.

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

Future development may include:

- Equivalent Circuit Model (ECM) parameter identification and validation.
- Physics-informed battery degradation modelling.
- Integration with electrochemical simulation frameworks such as PyBaMM.
- Improved temporal validation and uncertainty quantification.
- Online state updates using incoming battery measurements.
- Integration with battery management and monitoring systems.

## Author

Developed as an independent research and technical portfolio project exploring explainable Digital Twins, battery health monitoring, and predictive modelling.
