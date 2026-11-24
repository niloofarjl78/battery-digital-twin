# Battery Digital Twin – Experimental Results

This directory contains the experimental outputs of the battery health monitoring and Digital Twin prototype. The results cover State-of-Health (SoH) estimation, cross-battery model validation, impedance-based health indicators, post-discharge voltage relaxation, and residual analysis.

The experiments use four lithium-ion batteries from the NASA Battery Data Set: B0005, B0006, B0007, and B0018.

## Overview of the Experiments

The analysis investigates three main questions:

- How accurately can battery SoH be estimated from operational electrical and thermal measurements?
- Do impedance-related indicators provide additional information about battery degradation?
- Can a hybrid estimation strategy maintain SoH monitoring when impedance measurements are unavailable?

The models are evaluated using **Leave-One-Battery-Out (LOBO) validation**. In each experiment, three batteries are used for training and the remaining battery is held out for testing. This evaluates how well the models generalise to a battery that was not included in the training data.

## SoH Definition

The reference State of Health is calculated from the measured discharge capacity:

\[
\mathrm{SoH}_i =
\frac{Q_i}{Q_{\mathrm{initial}}}\times 100
\]

where:

- \(Q_i\) is the measured discharge capacity at observation \(i\), in Ah.
- \(Q_{\mathrm{initial}}\) is the first measured discharge capacity of the same battery.

SoH is therefore normalised independently for each battery. It represents a capacity-based health indicator rather than a direct measurement of all battery degradation mechanisms.

## Model Evaluation Metrics

The models are evaluated using Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and the coefficient of determination (\(R^2\)).

\[
\mathrm{MAE} =
\frac{1}{n}\sum_{i=1}^{n}
|y_i-\hat{y}_i|
\]

\[
\mathrm{RMSE} =
\sqrt{\frac{1}{n}\sum_{i=1}^{n}
(y_i-\hat{y}_i)^2}
\]

\[
R^2 =
1-
\frac{\sum_{i=1}^{n}(y_i-\hat{y}_i)^2}
{\sum_{i=1}^{n}(y_i-\bar{y})^2}
\]

where \(y_i\) is the measured SoH, \(\hat{y}_i\) is the estimated SoH, and \(\bar{y}\) is the mean measured SoH.

MAE and RMSE are expressed in SoH percentage points. Lower values indicate smaller prediction errors, while higher \(R^2\) values indicate a better fit to the observed variation.

## Baseline SoH Estimation

The baseline model uses operational electrical and thermal features:

- Mean, minimum, and maximum voltage
- Mean current
- Mean and maximum temperature
- Temperature rise during discharge

A Ridge Regression model with feature standardisation is used to estimate SoH.

The discharge duration is excluded from the prediction features because it is directly related to the capacity calculation and could introduce target leakage.

### Leave-One-Battery-Out Results

| Held-out battery | Training observations | Test observations | MAE | RMSE | R² |
|---|---:|---:|---:|---:|---:|
| B0005 | 468 | 168 | 1.7923 | 2.5010 | 0.9402 |
| B0006 | 468 | 168 | 3.4296 | 4.2320 | 0.8825 |
| B0007 | 468 | 168 | 4.2123 | 4.9917 | 0.6540 |
| B0018 | 504 | 132 | 1.5000 | 1.7369 | 0.9564 |

The baseline model performs differently across the four batteries. B0018 and B0005 show relatively low estimation errors, whereas B0007 is more challenging to predict.

This variation highlights the difficulty of transferring a model between batteries with different degradation trajectories and operating characteristics.

## Contribution of Impedance Indicators

The NASA dataset provides two impedance-related indicators:

- \(R_e\): estimated electrolyte resistance.
- \(R_{ct}\): estimated charge-transfer resistance.

These indicators are matched to discharge observations using their timestamps. Only impedance measurements within the specified one-hour backward matching tolerance are retained. Missing impedance measurements are preserved rather than replaced with zero.

A comparison was conducted using the same matched observations for both models:

- **Model A:** operational electrical and thermal features.
- **Model B:** the same features, augmented with \(R_e\) and \(R_{ct}\).

### Matched-Observation Comparison

| Battery | Model A MAE | Model B MAE | Change in MAE |
|---|---:|---:|---:|
| B0005 | 1.4376 | 1.7044 | +18.56% |
| B0006 | 2.9777 | 2.8362 | −4.75% |
| B0007 | 3.6999 | 1.3230 | −64.24% |
| B0018 | 2.5204 | 4.1715 | +65.51% |
| **Weighted overall** | **2.6949** | **2.0762** | **−22.96%** |

The weighted MAE is calculated by aggregating absolute prediction errors across all matched test observations, rather than taking an unweighted average of the battery-level MAEs.

The results indicate that impedance indicators can provide useful additional information, but their contribution is not consistent across batteries. The improvement is particularly strong for B0007, while the augmented model performs worse for B0005 and B0018.

An additional feature-ablation analysis suggests that \(R_e\) contributes more consistently than \(R_{ct}\) in this dataset. However, the results do not establish that either indicator will improve estimation for every battery.

## Hybrid SoH Estimation

To address incomplete impedance availability, a hybrid estimation strategy was investigated.

The strategy uses:

- Model A when impedance measurements are unavailable.
- Model B when impedance measurements are available.

This allows the Digital Twin to continue producing SoH estimates without requiring a complete impedance record for every discharge observation.

### Hybrid Model Results

| Held-out battery | Model A only MAE | Hybrid MAE | MAE improvement |
|---|---:|---:|---:|
| B0005 | 2.0666 | 2.2762 | −10.14% |
| B0006 | 3.2443 | 3.1331 | +3.43% |
| B0007 | 4.5667 | 2.6991 | +40.90% |
| B0018 | 1.7862 | 2.0739 | −16.11% |
| **Weighted overall** | **2.9799** | **2.5723** | **+13.68%** |

The hybrid strategy reduces the weighted overall MAE by approximately 13.68% compared with the baseline-only strategy under this evaluation setup.

However, the effect varies across batteries. The hybrid model improves the results for B0006 and B0007, but slightly worsens them for B0005 and B0018.

The comparison should also be interpreted in light of the non-uniform availability of impedance measurements. In particular, B0018 has substantially fewer matched impedance observations than the other batteries.

## Post-Discharge Voltage Relaxation

The discharge records were further investigated to examine voltage recovery after the discharge current is interrupted.

The post-discharge voltage response was approximated using a single-exponential relaxation model:

\[
V(t)=V_{\infty}+A\exp\left(-\frac{t}{\tau}\right)
\]

where:

- \(V(t)\) is the measured voltage after discharge termination.
- \(V_{\infty}\) is the fitted asymptotic voltage.
- \(A\) is the fitted voltage relaxation amplitude.
- \(\tau\) is the relaxation time constant, in seconds.
- \(t\) is elapsed time after discharge termination.

The model was fitted independently to individual discharge records. The resulting parameters
