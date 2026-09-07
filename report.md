# International Air Passengers — Forecasting Report

## 1. The recommendation

I would ship **SeasonalNaive** as the forecasting model.

The rolling-origin harness evaluated the shortlisted models across multiple forecasting origins and showed that SeasonalNaive performed better than AutoETS on the main accuracy and distributional metrics. SeasonalNaive achieved a **MASE of 1.313310**, compared with **2.061432** for AutoETS. It also achieved a lower **RMSE of 1.245572** compared with **2.389743**, and a lower **scaled CRPS of 0.061603** compared with **0.105503**.

These results make SeasonalNaive the stronger model for the current forecasting task. Its MASE across the rolling-origin evaluations ranged from **0.411584 to 1.958726**, while AutoETS ranged from **1.421879 to 3.196465**. Therefore, SeasonalNaive provides the better overall point-forecast performance and distributional score in the harness.

The recommendation is therefore to use SeasonalNaive as the production baseline for this forecasting task, while treating its prediction intervals with caution.

## 2. The intervals

The point forecast results do not tell the whole story. The forecasting intervals must also be checked to determine whether the uncertainty estimates are reliable.

For the nominal **80% prediction interval**, SeasonalNaive achieved an empirical coverage of **0.510417**. This means that only about 51% of the observed held-out values fell inside an interval that was intended to contain 80% of future observations.

This indicates that the SeasonalNaive intervals are **too narrow** and are therefore not honest 80% uncertainty bands. The model has good relative performance on the point and distributional metrics, but its uncertainty estimates substantially underestimate the actual forecast uncertainty.

AutoETS produced a different result. Its 80% coverage was **0.812500**, which is much closer to the nominal 80% target. This means that its intervals are considerably better calibrated in terms of coverage.

However, AutoETS achieves this better coverage while performing worse on the main accuracy metrics. Its MASE, RMSE, and scaled CRPS are all higher than those of SeasonalNaive.

Therefore, the two models show an important trade-off: **SeasonalNaive is better for forecast accuracy, while AutoETS provides more appropriate 80% interval coverage**. The coverage result should not be ignored simply because SeasonalNaive wins the point forecast. If the forecast is used for decisions where uncertainty matters, the intervals require further calibration.

## 3. The residuals

The residual diagnostics show that there is still important temporal structure left in the forecasting errors.

The Ljung-Box test produced a p-value of **2.319678e-43** at lag 12 and **1.711303e-44** at lag 24. Both p-values are extremely small, providing strong evidence against the hypothesis that the residuals are free of autocorrelation at these lags.

In practical terms, the forecast errors are not behaving like independent random noise. Previous observations still contain information that is related to the current forecasting error. This indicates that the model has not captured all of the temporal structure in the monthly passenger series.

This result is important because a model can achieve a good headline accuracy score while still leaving systematic structure in its residuals. For SeasonalNaive, the Ljung-Box results therefore suggest that there is room for a more sophisticated model that can capture additional temporal dependence.

The residual diagnostics also reinforce the need to distinguish between forecast accuracy and model completeness. SeasonalNaive is the best model among the shortlisted models according to the harness metrics, but its residuals indicate that the forecasting problem has not been completely solved.

## 4. One change

The next change I would make is to **improve the calibration of the prediction intervals while preserving the leakage-safe rolling-origin evaluation**.

The main reason for this change is the gap between the nominal 80% interval and the observed SeasonalNaive coverage of **0.510417**. The model's point forecasts perform better than AutoETS, but its uncertainty intervals are too narrow.

A practical next step would be to use historical out-of-sample residuals from the rolling-origin procedure to calibrate the prediction intervals. The calibration should be performed separately within each training fold so that no future observations are used when estimating the uncertainty for that fold.

The expected result is wider and better-calibrated prediction intervals, with empirical coverage moving closer to the nominal 80% level while retaining SeasonalNaive's strong point-forecast performance.

This would address the most important weakness identified by the harness without replacing the model that currently performs best on MASE, RMSE, and scaled CRPS.
