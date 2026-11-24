# Results and Model Evaluation

This directory contains the experimental outputs, model evaluation results, and diagnostic analyses produced during the development of the Explainable Battery Digital Twin.

The results investigate the relationship between battery operating measurements, degradation indicators, and State-of-Health (SoH) estimation across four lithium-ion battery cells from the NASA Battery Aging Dataset.

## Main Findings

### Battery Health Estimation

A Ridge Regression baseline model was evaluated using a Leave-One-Battery-Out (LOBO) validation strategy. In each experiment, three batteries were used for training and the remaining battery was held out for testing.

| Held-out battery | MAE (%) | RMSE (%) | R² |
|---|---:|---:|---:|
| B0005 | 1.79 | 2.50 | 0.940 |
| B0006 | 3.43 | 4.23 | 0.883 |
| B0007 | 4.21 | 4.99 | 0.654 |
| B0018 | 1.50 | 1.74 | 0.956 |

The results demonstrate that the baseline model can estimate SoH with varying levels of accuracy across different battery cells, highlighting the challenge of cross-battery generalisation.

### Contribution of Impedance Indicators

The contribution of electrochemical impedance indicators (Re and Rct) was evaluated by comparing models trained and tested on the same impedance-matched observations.

Adding impedance indicators reduced the weighted MAE from 2.69% to 2.08%, corresponding to an improvement of approximately 22.96%.

However, the improvement was not consistent across all batteries. This suggests that impedance information can provide additional diagnostic value, but its contribution depends on the battery and degradation behaviour.

### Hybrid SoH Estimation

A hybrid estimation strategy was investigated to address incomplete impedance availability.

The framework uses:
- An impedance-augmented model when impedance measurements are available.
- A baseline model as a fallback when impedance measurements are missing.

Across all held-out observations, the hybrid approach achieved a weighted MAE of 2.57%, compared with 2.98% for the baseline model trained on the same matched training observations. This represents an overall reduction of approximately 13.68%.

The results also reveal differences in performance across batteries, reinforcing the importance of handling missing measurements and battery-specific degradation patterns.

### Resistance-Based Degradation Indicators

Post-discharge voltage relaxation was analysed by fitting an exponential relaxation model:

\[
V(t)=V_{\infty}+A e^{-t/\tau}
\]

The fitted parameters were used to investigate relaxation behaviour and derive an apparent resistance proxy:

\[
R_{\mathrm{app}}=\frac{A}{I_{\mathrm{before\ cutoff}}}
\]

The apparent resistance proxy showed strong negative correlations with SoH across all four batteries. However, incorporating this indicator into the cross-battery prediction model did not improve overall generalisation.

This quantity is treated as a diagnostic proxy, not as a validated electrochemical resistance parameter.

## Evaluation Methodology

The modelling experiments include:

- Leave-One-Battery-Out (LOBO) validation for cross-battery generalisation.
- Mean Absolute Error (MAE), Root Mean Squared Error (RMSE), and coefficient of determination (R²).
- Matched-observation comparisons for evaluating impedance contributions.
- Hybrid estimation with baseline fallback for missing impedance measurements.
- Residual analysis to identify systematic estimation errors.
- Correlation and trend-adjusted analyses of degradation indicators.
- Relaxation parameter extraction and apparent resistance investigation.

## Repository Contents

| File | Description |
|---|---|
| `modelling_summary.md` | Consolidated methodology, experiments, quantitative findings, and conclusions |
| `overall_model_performance.csv` | Baseline model performance across held-out batteries |
| `model_A_vs_hybrid.csv` | Comparison of baseline and hybrid estimation |
| `impedance_contribution_analysis.csv` | Evaluation of the contribution of Re and Rct |
| `residual_statistics.csv` | Residual statistics across batteries and model types |
| `lobo_relaxation_overall_results.csv` | Cross-battery evaluation of relaxation-based features |
| `revised_resistance_model_summary.csv` | Performance comparison using the revised apparent resistance proxy |
| `revised_resistance_correlations.csv` | Correlation analysis between apparent resistance and SoH |
| `hybrid_soh_validation.png` | Actual versus estimated SoH for the hybrid framework |
| `hybrid_soh_residuals.png` | Residual analysis of hybrid SoH predictions |
| `revised_resistance_vs_soh.png` | Relationship between apparent resistance and SoH |

## Interpretation and Limitations

The results indicate that combining operational measurements with impedance information can improve SoH estimation under certain conditions. Nevertheless, the contribution of individual indicators varies across battery cells.

Several limitations should be considered:

- The dataset contains only four battery cells.
- Impedance measurements are not uniformly available across all discharge cycles.
- Battery-specific discharge cut-off voltages differ.
- LOBO validation evaluates generalisation across the available cells, but does not establish performance on an independent battery population.
- Relaxation-derived parameters and apparent resistance proxies require further physical validation.
- The current framework is a prototype and does not yet constitute a fully validated electrochemical Digital Twin.

Overall, these experiments provide a foundation for developing an interpretable, data-driven battery Digital Twin that combines operational measurements, degradation indicators, and machine-learning-based health estimation.
