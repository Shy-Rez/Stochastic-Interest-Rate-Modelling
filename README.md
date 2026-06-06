# Stochastic Interest Rate Modelling: CIR & CIR++

Predicts the full yield curve (6M–30Y) from only the 3-Month short rate using the CIR model and a Ridge Regression correction layer.

---

## Summary

The CIR model assumes short rate dynamics follow:

$$dr_t = \kappa(\theta - r_t)dt + \sigma\sqrt{r_t}dW_t$$

The affine yield structure means the entire curve is a closed-form function of today's 3M rate alone, making single-input reconstruction possible. The CIR++ extension adds a learned correction to fix the base model's systematic biases.

---

## Results

| Model | 6M | 9M | 1Y | 2Y | Overall R² |
|---|---|---|---|---|---|
| OLS | 0.982 | 0.918 | 0.794 | −0.419 | 0.748  |
| MLE | 0.979 | 0.910 | 0.777 | −0.520 | 0.729  |
| Base CIR | 0.994 | 0.967 | 0.910 | 0.389 | 0.893  |
| CIR++ | 0.992 | 0.958 | 0.888 | 0.540 | 0.903  |

Target: R2 ≥ 0.85 out-of-sample. Evaluation on 6M–2Y maturities. 5Y–30Y are reconstruction outputs only.

---

## Methodology

**Calibration** — Three methods tested. Cross-sectional optimisation is selected as it directly minimises yield-curve pricing errors, matching the evaluation objective. OLS and MLE only fit the 3M time series and fail at long maturities.

**CIR++** — Ridge Regression is trained on the base model's training residuals per maturity. High regularisation (alpha = 5000) at short maturities forces the correction to near-zero where the base model already performs well; lower alpha at 2Y–30Y allows active bias correction.

---

## Repository Structure

```
├── CIR_IR_Model.ipynb     # Main notebook (Phases A–F)
├── train_data.csv         # 1,976 days, 9 maturities (3M–30Y)
├── test_data.csv          # 495 days, maturities 3M–2Y
├── test_data_3M.csv       # 495 days, 3M rate only (model input)
└──images                  # Contains diagrams associated with model evaluation 

```

---

## Dependencies

```bash
pip install numpy pandas matplotlib seaborn scipy scikit-learn
```

---

## References

- Cox, Ingersoll, Ross (1985). *A Theory of the Term Structure of Interest Rates.* Econometrica.
- Brigo, Mercurio (2006). *Interest Rate Models — Theory and Practice.* Springer.