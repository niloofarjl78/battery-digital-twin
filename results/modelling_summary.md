
# Battery Digital Twin – Modelling Results

## 1. Dataset
- NASA Battery Aging Dataset
- Batteries: B0005, B0006, B0007, B0018
- Total discharge observations: 636
- Matched impedance observations: 419
- Health target: State of Health (SoH), calculated from
  discharge capacity relative to initial measured capacity.

## 2. Modelling objective
Investigate whether electrical and thermal indicators,
particularly impedance resistance (Re), provide useful
information for battery SoH estimation.

## 3. Temporal validation
A chronological 70/30 train-test split was used for
each battery.

| Model | Weighted MAE | Weighted RMSE |
|---|---:|---:|
| Cycle + Re | 3.0894 | 3.3447 |
| Cycle only | 3.6955 | 4.0590 |
| Polynomial | 4.2225 | 6.0997 |

Cycle + Re achieved a 16.4% reduction in weighted MAE
compared with Cycle only.

## 4. Multicollinearity
Training-set correlations between cycle number and Re:
- B0005: 0.9629
- B0006: 0.9851
- B0007: 0.9391
- B0018: 0.2091

VIF values:
- B0005: 13.74
- B0006: 33.83
- B0007: 8.47
- B0018: 1.05

These results indicate substantial multicollinearity
for the first three batteries.

## 5. Residual resistance experiment
Resistance was modelled as a function of cycle number.
The residual resistance was then used as an additional
predictor of SoH.

| Battery | Cycle MAE | Cycle + residual Re MAE |
|---|---:|---:|
| B0005 | 2.9301 | 2.9942 |
| B0006 | 5.1297 | 3.1757 |
| B0007 | 3.1091 | 3.1378 |
| B0018 | 3.2241 | 2.8648 |

The improvement was battery-dependent and was particularly
pronounced for B0006.

## 6. Main conclusions
- Cycle number provides a useful indicator of degradation.
- Re contains potentially useful additional information.
- The relationship between Re and SoH is not consistent
  across all four batteries.
- Multicollinearity complicates coefficient interpretation.
- Polynomial regression showed weaker temporal
  generalisation than the linear alternatives.
- The extended resistance-based model performed better
  overall, but further validation is necessary.

## 7. Limitations
- Only four battery cells were investigated.
- B0018 had only seven temporal test observations.
- Different discharge cut-off voltages affect
  cross-battery comparability.
- Temporal extrapolation is challenging because later
  degradation states differ from training conditions.
- The current model is empirical, not yet physics-informed.
- The current estimator uses cycle-level measurements
  after discharge, not pre-cycle forecasting.

## 8. Next development stage
Develop a Digital Twin architecture with:
- Battery state representation
- Cycle-by-cycle state updates
- Resistance-based health indicators
- SoH estimation component
- Monitoring and visualisation dashboard


## 14. Revised Apparent Resistance Estimation

An apparent resistance proxy was estimated using the fitted
post-discharge relaxation amplitude and the absolute discharge
current immediately before cutoff:

R_app_cutoff = A / I_before_cutoff

This quantity is treated as an apparent resistance indicator,
not as a directly measured ohmic resistance.

The revised estimates were:

| Battery | Mean apparent resistance (Ohm) |
|---|---:|
| B0005 | 0.2166 |
| B0006 | 0.2747 |
| B0007 | 0.2708 |
| B0018 | 0.2504 |

A total of 533 observations were available, with no missing,
duplicate, zero, or negative resistance values.

## 15. Revised Resistance and State of Health

The revised apparent resistance showed a strong negative
correlation with SoH:

| Battery | Pearson r | Spearman r |
|---|---:|---:|
| B0005 | -0.9299 | -0.9419 |
| B0006 | -0.8322 | -0.7140 |
| B0007 | -0.9680 | -0.9701 |
| B0018 | -0.9035 | -0.9287 |

After removing the linear trend associated with discharge
index, negative correlations remained:

| Battery | Detrended Pearson r | Detrended Spearman r |
|---|---:|---:|
| B0005 | -0.4426 | -0.3689 |
| B0006 | -0.7489 | -0.7729 |
| B0007 | -0.8389 | -0.7914 |
| B0018 | -0.7455 | -0.7478 |

These results indicate that the resistance–SoH association
is not explained entirely by a shared linear ageing trend.
However, correlation does not establish causality or validate
the proxy as a direct physical resistance measurement.

## 16. Predictive Evaluation of Revised Apparent Resistance

A leave-one-battery-out comparison was performed using the
same 533 observations for both models.

The baseline model used voltage, current, and temperature
features. The augmented model additionally included revised
apparent resistance.

| Model | Weighted MAE | Weighted RMSE |
|---|---:|---:|
| Baseline | 1.9474 | 2.7122 |
| Baseline + revised apparent resistance | 2.4433 | 3.1098 |

Adding revised apparent resistance increased weighted MAE
by 25.47% and weighted RMSE by approximately 14.66%.

The effect varied across batteries. Performance improved
for B0005, changed only slightly for B0006, and deteriorated
for B0007 and particularly B0018 in terms of MAE.

Therefore, revised apparent resistance is retained as a
diagnostic degradation indicator, but it is not selected
as an additional feature in the current cross-battery
predictive model.

## 17. Updated Interpretation and Limitations

The revised resistance experiment provides evidence of a
strong association between an apparent relaxation-derived
resistance indicator and battery health.

However, its predictive contribution does not generalise
consistently across the four batteries.

The current estimate is based on relaxation amplitude
divided by current immediately before discharge cutoff.
It is not an independently validated measurement of
instantaneous ohmic resistance.

Further validation would require more detailed electrical
measurements and a physically identifiable equivalent
circuit model.

The current baseline model remains the preferred
cross-battery predictive model based on the evaluated
weighted MAE and RMSE.


## 9. Matched-Observation Model Comparison

A leave-one-battery-out (LOBO) comparison was conducted
using the 419 observations with available impedance data.

Both models were trained on the same matched training
observations. Model A used electrical and thermal features,
while Model B additionally included Re and Rct.

| Model | Weighted MAE | Weighted RMSE |
|---|---:|---:|
| Model A | 2.6949 | 3.5835 |
| Model B | 2.0762 | 2.8238 |

Model B achieved a 22.96% reduction in weighted MAE.

However, the improvement varied across batteries,
indicating that impedance indicators do not provide
a uniform benefit.

## 10. Hybrid SoH Estimator

A hybrid estimation architecture was implemented to
handle incomplete impedance measurements.

- Model A: electrical and thermal features; used as fallback.
- Model B: electrical, thermal and impedance features;
  used when impedance measurements are available.

The estimator was evaluated using leave-one-battery-out
validation across all 636 discharge observations.

| Battery | Observations | Model A | Model B | MAE |
|---|---:|---:|---:|---:|
| B0005 | 168 | 36 | 132 | 2.2762 |
| B0006 | 168 | 36 | 132 | 3.1331 |
| B0007 | 168 | 36 | 132 | 2.6991 |
| B0018 | 132 | 109 | 23 | 2.0739 |

The overall comparison was:

| Model | Overall MAE |
|---|---:|
| Model A only | 2.9799 |
| Hybrid estimator | 2.5723 |

The hybrid estimator achieved a 13.68% reduction
in overall MAE.

## 11. Impedance Contribution Analysis

A separate comparison evaluated Model A and Model B
on identical impedance-matched test observations.

| Battery | Model A MAE | Model B MAE | Improvement |
|---|---:|---:|---:|
| B0005 | 1.4376 | 1.7044 | -18.56% |
| B0006 | 2.9777 | 2.8362 | 4.75% |
| B0007 | 3.6999 | 1.3230 | 64.24% |
| B0018 | 2.5204 | 4.1715 | -65.51% |

Impedance features substantially improved performance
for B0007, provided a modest improvement for B0006,
and deteriorated performance for B0005 and B0018.

These findings demonstrate battery-dependent predictive
value of impedance indicators.

## 12. Residual Analysis

Residuals were calculated as:

Estimated SoH minus Actual SoH.

Positive residuals indicate overestimation, while
negative residuals indicate underestimation.

The analysis identified:

- B0005: positive bias in Model B predictions.
- B0006: near-zero mean residual for Model B.
- B0007: substantially reduced underestimation with Model B.
- B0018: stronger negative bias for Model B.

The residual distributions also showed systematic
changes over the battery lifecycle.

This suggests that the current linear Ridge regression
models do not fully capture the degradation dynamics.

## 13. Updated Conclusions

The experiments indicate that:

- Electrical and thermal measurements provide a useful
  baseline for cycle-level SoH estimation.
- Impedance indicators can improve estimation accuracy,
  but their contribution varies between batteries.
- The hybrid architecture provides a practical approach
  to handling missing impedance observations.
- The hybrid estimator achieved a 13.68% overall MAE
  improvement relative to the matched-trained Model A baseline.
- Systematic residual patterns indicate that further
  model development and validation are necessary.

The current implementation is an offline, historical
battery health-monitoring prototype. It is not yet a
real-time connected Digital Twin.



## 14. Revised Apparent Resistance Estimation

An apparent resistance proxy was estimated using the fitted
post-discharge relaxation amplitude and the absolute discharge
current immediately before cutoff:

R_app_cutoff = A / I_before_cutoff

This quantity is treated as an apparent resistance indicator,
not as a directly measured ohmic resistance.

The revised estimates were:

| Battery | Mean apparent resistance (Ohm) |
|---|---:|
| B0005 | 0.2166 |
| B0006 | 0.2747 |
| B0007 | 0.2708 |
| B0018 | 0.2504 |

A total of 533 observations were available, with no missing,
duplicate, zero, or negative resistance values.

## 15. Revised Resistance and State of Health

The revised apparent resistance showed a strong negative
correlation with SoH:

| Battery | Pearson r | Spearman r |
|---|---:|---:|
| B0005 | -0.9299 | -0.9419 |
| B0006 | -0.8322 | -0.7140 |
| B0007 | -0.9680 | -0.9701 |
| B0018 | -0.9035 | -0.9287 |

After removing the linear trend associated with discharge
index, negative correlations remained:

| Battery | Detrended Pearson r | Detrended Spearman r |
|---|---:|---:|
| B0005 | -0.4426 | -0.3689 |
| B0006 | -0.7489 | -0.7729 |
| B0007 | -0.8389 | -0.7914 |
| B0018 | -0.7455 | -0.7478 |

These results indicate that the resistance–SoH association
is not explained entirely by a shared linear ageing trend.
However, correlation does not establish causality or validate
the proxy as a direct physical resistance measurement.

## 16. Predictive Evaluation of Revised Apparent Resistance

A leave-one-battery-out comparison was performed using the
same 533 observations for both models.

The baseline model used voltage, current, and temperature
features. The augmented model additionally included revised
apparent resistance.

| Model | Weighted MAE | Weighted RMSE |
|---|---:|---:|
| Baseline | 1.9474 | 2.7122 |
| Baseline + revised apparent resistance | 2.4433 | 3.1098 |

Adding revised apparent resistance increased weighted MAE
by 25.47% and weighted RMSE by approximately 14.66%.

The effect varied across batteries. Performance improved
for B0005, changed only slightly for B0006, and deteriorated
for B0007 and particularly B0018 in terms of MAE.

Therefore, revised apparent resistance is retained as a
diagnostic degradation indicator, but it is not selected
as an additional feature in the current cross-battery
predictive model.

## 17. Updated Interpretation and Limitations

The revised resistance experiment provides evidence of a
strong association between an apparent relaxation-derived
resistance indicator and battery health.

However, its predictive contribution does not generalise
consistently across the four batteries.

The current estimate is based on relaxation amplitude
divided by current immediately before discharge cutoff.
It is not an independently validated measurement of
instantaneous ohmic resistance.

Further validation would require more detailed electrical
measurements and a physically identifiable equivalent
circuit model.

The current baseline model remains the preferred
cross-battery predictive model based on the evaluated
weighted MAE and RMSE.
