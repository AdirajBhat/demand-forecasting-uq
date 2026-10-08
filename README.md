# demand-forecasting-uq
Single-point sales forecasts hide real risk when stockouts cost more than excess inventory. This project builds a calibrated quantile forecasting model (10th/50th/90th percentiles) on retail data to optimize safety stock under asymmetric costs, diagnose overconfidence during demand spikes, and prove intervals beat naive point guesses
 
 
# 📦 Demand Forecasting with Uncertainty Quantification

A machine learning project that replaces traditional point forecasts with calibrated prediction intervals (10th, 50th, and 90th percentiles), directly enabling risk-aware inventory replenishment under asymmetric stockout vs. holding costs.

---

## 🎯 Project Overview

Standard forecasting models predict a single expected value (e.g., *"tomorrow's sales will be 45 units"*). In reality, demand is uncertain, and running out of stock is typically far more expensive than carrying excess inventory.

This project implements **Quantile LightGBM** to output an 80% prediction interval (`[q10, q90]`) alongside a median point estimate (`q50`). We evaluate both statistical coverage calibration and demonstrate the business value of ordering based on upper-quantile safety stock.

---

## ✨ Key Features

- **Time-Series Feature Engineering:** Generates multi-step lags (1, 7, 14 days), rolling averages, rolling volatility (std), calendar signals (day of week, month, weekend), and SNAP event indicators without lookahead leakage.
- **Quantile Regression (LightGBM):** Directly models the 10th, 50th, and 90th conditional percentiles using Pinball (Quantile) loss.
- **Interval Calibration:** Assesses whether ~80% of true sales fall between `q10` and `q90`.
- **Asymmetric Cost Simulation:** Tests inventory strategies under asymmetric cost penalties ($5 stockout vs. $1 holding cost per unit).
- **Spike & Overconfidence Diagnostics:** Identifies where prediction intervals run too narrow during sudden demand surges.
- **Baseline Comparison (Bonus):** Benchmarks quantile regression against a standard point forecast with static Gaussian residual bounds ($z \cdot \sigma$).

---

## 📊 Summary of Results

### 1. Calibration vs. Naive Baseline
| Metric | Quantile LightGBM | Naive Residual Baseline |
| :--- | :---: | :---: |
| **Point Accuracy (MAE)** | **1.12** | 1.15 |
| **Point Accuracy (RMSE)** | **2.04** | 2.01 |
| **Target Coverage** | **80.0%** | 80.0% |
| **Empirical Coverage** | **~79.4%** | ~71.2% |
| **Status** | **Well-Calibrated** | Undercovered (Overconfident) |

*Key Takeaway:* Quantile LightGBM dynamically expands intervals during high-variance periods (e.g., weekends), while the static residual approach fails to adapt, missing the 80% coverage target.

### 2. Business Inventory Payoff ($5 Stockout vs. $1 Holding Cost)
- **Median Ordering (`q50`):** Minimizes storage costs but frequently runs out of stock, incurring high penalty costs.
- **Safety Stock Ordering (`q90`):** Absorbs modest extra holding costs while eliminating >85% of stockout days, reducing net inventory loss by **~40–50%**.

### 3. Overconfidence on Demand Spikes
- **Normal Days Breach Rate:** ~8.4%
- **Spike Days Breach Rate (>1.5× 7-day average):** ~28.6%
- *Insight:* Rolling features are inherently backward-looking; unannounced sudden surges cause localized interval breaches, highlighting the need for forward promotional flags.

---

## 🛠️ Project Structure

### text:
demand-forecasting-uq/
├── calendar.csv                  # Calendar events and SNAP indicators
├── sales_train_validation.csv    # Daily sales histories (M5 / Kaggle)
├── Demand_Forecasting_UQ.ipynb   # Complete pipeline & evaluation notebook
├── README.md                     # Project documentation
└── requirements.txt              # Dependencies
