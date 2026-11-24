## Results

This section summarises the main findings from battery health estimation, impedance integration, hybrid Digital Twin modelling, and the investigation of relaxation-based degradation indicators.

### Baseline SoH Estimation

A Ridge Regression model using electrical and thermal measurements was evaluated through Leave-One-Battery-Out (LOBO) validation. Each battery was held out once for testing, while the remaining three batteries were used for training.

The baseline model achieved the following results:

| Test Battery | Test Observations | MAE (percentage points) | RMSE (percentage points) | $R^2$ |
|---|---:|---:|---:|---:|
| B0005 | 168 | 1.7923 | 2.5010 | 0.9402 |
| B0006 | 168 | 3.4296 | 4.2320 | 0.8825 |
| B0007 | 168 | 4.2123 | 4.9917 | 0.6540 |
| B0018 | 132 | 1.5000 | 1.7369 | 0.9564 |

The results indicate that the baseline model can estimate SoH with relatively low errors for B0005, B0006, and B0018. However, performance is weaker for B0007, suggesting that the relationship between operational measurements and battery health does not generalise equally well across all battery cells.

### Contribution of Impedance Measurements

The contribution of impedance measurements was investigated by comparing two models:

- **Model A – Baseline:** electrical and thermal operational features.
- **Model B – Impedance-augmented:** the same features supplemented with the measured electrolyte resistance ($R_e$) and charge-transfer resistance ($R_{ct}$).

To ensure a fair comparison, both models were trained and evaluated using the same observations for which impedance measurements were available. This resulted in 419 matched observations across the four batteries.

| Test Battery | Model A MAE | Model B MAE | Change in MAE |
|---|---:|---:|---:|
| B0005 | 1.4376 | 1.7044 | 18.56% worse |
| B0006 | 2.9777 | 2.8362 | 4.75% better |
| B0007 | 3.6999 | 1.3230 | 64.24% better |
| B0018 | 2.5204 | 4.1715 | 65.51% worse |
| **Weighted average** | **2.6949** | **2.0762** | **22.96% better** |

The impedance-augmented model achieved a 22.96% reduction in weighted MAE across the matched observations. However, its contribution varied substantially between batteries: performance improved for B0006 and B0007 but deteriorated for B0005 and B0018.

An additional feature-ablation analysis indicated that $R_e$ provided a stronger overall contribution than $R_{ct}$ in this dataset. Combining both impedance variables did not consistently improve performance over using $R_e$ alone.

These findings suggest that impedance measurements can provide useful complementary information for battery health estimation, but their predictive contribution is battery-dependent.

### Hybrid Digital Twin SoH Estimation

A hybrid estimation strategy was developed to account for the incomplete availability of impedance measurements.

The approach combines two models:

- Model A estimates SoH using electrical and thermal measurements and serves as the fallback model.
- Model B incorporates impedance measurements when they are available.

For each observation, the hybrid framework uses the impedance-augmented estimate when a valid impedance measurement is available; otherwise, it retains the baseline estimate.

The evaluation used the same matched-observation training population for both models, while predictions were generated for all observations of each held-out battery.

| Test Battery | Total Observations | Baseline MAE | Hybrid MAE | MAE Improvement |
|---|---:|---:|---:|---:|
| B0005 | 168 | 2.0666 | 2.2762 | -10.14% |
| B0006 | 168 | 3.2443 | 3.1331 | +3.43% |
| B0007 | 168 | 4.5667 | 2.6991 | +40.90% |
| B0018 | 132 | 1.7862 | 2.0739 | -16.11% |
| **Weighted average** | **636** | **2.9799** | **2.5723** | **+13.68%** |

The hybrid strategy reduced the overall weighted MAE by 13.68% compared with the baseline evaluated on the same test observations. The largest improvement occurred for B0007, whereas performance deteriorated for B0005 and B0018.

This demonstrates the potential of a Digital Twin architecture that can incorporate additional diagnostic measurements when available while maintaining operation through a baseline estimation model when they are missing. Nevertheless, the results also show that adding impedance information does not guarantee improved estimation for every battery.

### Impedance Availability and Prediction Reliability

Impedance measurements were not available for every discharge observation.

| Battery | Discharge Observations | Matched Impedance Observations | Coverage |
|---|---:|---:|---:|
| B0005 | 168 | 132 | 78.57% |
| B0006 | 168 | 132 | 78.57% |
| B0007 | 168 | 132 | 78.57% |
| B0018 | 132 | 23 | 17.42% |
| **Total** | **636** | **419** | **65.88%** |

The availability analysis reveals an important limitation: impedance coverage differs substantially between batteries, particularly for B0018.

Furthermore, the unmatched observations do not necessarily represent a random subset of the dataset. Therefore, the performance of the impedance-augmented model on matched observations should not be interpreted as evidence that impedance will improve estimation across all operating conditions.

The hybrid architecture addresses this limitation by preserving baseline predictions when impedance measurements are unavailable.

### Residual Analysis

Prediction residuals were calculated as:

$$
e_i = \widehat{\mathrm{SoH}}_i - \mathrm{SoH}_i
$$

Positive residuals indicate SoH overestimation, while negative residuals indicate underestimation.

The residual analysis revealed different error patterns across the batteries:

- **B0005:** The baseline model tended to underestimate SoH on unmatched observations, while the impedance-augmented model showed some overestimation during later degradation stages.
- **B0006:** Prediction errors varied across degradation stages, with the impedance-augmented model showing a smaller overall bias on the matched observations.
- **B0007:** The impedance-augmented model substantially reduced the errors observed with the baseline model on matched observations.
- **B0018:** The impedance-augmented model showed higher errors, although impedance coverage was particularly limited for this battery.

These findings highlight the importance of evaluating prediction errors at battery and degradation-stage levels rather than relying exclusively on aggregate performance metrics.

### Investigation of Relaxation-Based Health Indicators

Post-discharge voltage relaxation was investigated as an additional source of degradation-related information.

Following discharge termination, the measured voltage was fitted using a single-exponential relaxation model:

$$
V(t) = V_{\infty} + A \exp\left(-\frac{t-t_0}{\tau}\right)
$$

where:

- $V_{\infty}$ represents the fitted long-term voltage level.
- $A$ represents the fitted voltage relaxation amplitude.
- $\tau$ represents the relaxation time constant.
- $t_0$ is the beginning of the post-discharge relaxation period.

The fitted parameters showed associations with measured SoH across several batteries. However, their relationships were not consistent across all cells, and the parameters were also correlated with one another.

A cross-battery model comparison was conducted using 533 observations with available relaxation parameters. Adding the relaxation parameters to the operational baseline features did not improve the overall weighted MAE:

| Model Configuration | Weighted MAE |
|---|---:|
| Electrical and thermal baseline | 1.9474 |
| Baseline + relaxation time constant ($\tau$) | 2.4161 |
| Baseline + relaxation amplitude ($A$) | 2.4648 |
| Baseline + fitted final voltage ($V_{\infty}$) | 2.2161 |
| Baseline + all three relaxation parameters | 3.8285 |

Although some relaxation indicators improved performance for individual batteries, their addition did not improve generalisation across the complete battery set under the current modelling configuration.

### Apparent Resistance as a Diagnostic Indicator

An additional apparent resistance proxy was investigated using the fitted relaxation amplitude and the current immediately before discharge termination:

$$
R_{\mathrm{app}} = \frac{A}{I_{\mathrm{cutoff}}}
$$

where $I_{\mathrm{cutoff}}$ is the absolute current immediately before discharge termination.

The apparent resistance proxy showed negative correlations with SoH across all four batteries:

| Battery | Pearson Correlation with SoH | Spearman Correlation with SoH |
|---|---:|---:|
| B0005 | -0.9299 | -0.9419 |
| B0006 | -0.8322 | -0.7140 |
| B0007 | -0.9680 | -0.9701 |
| B0018 | -0.9035 | -0.9287 |

These associations indicate that the apparent resistance proxy increases as capacity-based SoH decreases in the investigated observations.

However, incorporating this indicator into the cross-battery prediction model did not improve overall performance. The weighted MAE increased from 1.9474 to 2.4433 percentage points.

The indicator is therefore retained as a diagnostic feature for further investigation rather than as a validated physical resistance parameter or a selected predictive feature.

Importantly, $R_{\mathrm{app}}$ is an empirical proxy derived from post-discharge relaxation. It should not be interpreted as a directly measured ohmic resistance or as an identified electrochemical equivalent-circuit parameter.

### Main Findings

The experiments lead to the following conclusions:

1. Electrical and thermal operational measurements provide a useful baseline for capacity-based SoH estimation, although generalisation performance varies across batteries.
2. Impedance measurements can improve SoH estimation on matched observations, but their contribution is not consistent across battery cells.
3. A hybrid estimation strategy can accommodate incomplete impedance availability and achieved a 13.68% reduction in overall weighted MAE compared with the baseline in the evaluated experiment.
4. Post-discharge relaxation parameters and the apparent resistance proxy show degradation-related associations, but they did not improve overall cross-battery prediction performance in the current experiments.
5. Missing measurements, differences between battery cells, and temporal degradation patterns remain important challenges for reliable battery health estimation.

Overall, the results support the feasibility of an interpretable, data-driven Digital Twin prototype that combines operational measurements, diagnostic indicators, and machine-learning-based SoH estimation. Further work is required to establish physically validated equivalent-circuit parameters, improve temporal generalisation, and evaluate the framework under additional operating conditions.
