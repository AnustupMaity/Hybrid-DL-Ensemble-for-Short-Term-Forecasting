# Hybrid Deep Learning Ensemble for Short-Term Forecasting - Codebase

This repository implements a **hybrid deep learning ensemble approach** for short-term inflow and discharge forecasting, as detailed in the associated research paper. The project combines multiple modeling strategies to achieve robust and accurate predictions, especially for extreme and peak events.

---

## 📁 Dataset Files

- **`Data_raw_inflow.csv`**: Raw inflow data used for training and evaluation.  
- **`Discharge_data_Raw.csv`**: Raw discharge data.  
- **`Final_Processed_Dataset.csv`**: Fully processed dataset combining inflow and discharge features, ready for model training.  

---

## 📓 Notebooks

### 1. `NotebookML--TRIAL1.ipynb`  
- Implements **traditional ML models** (Random Forest, XGBoost, etc.) for forecasting.  
- Provides baseline performance but struggles with complex temporal dependencies and peak event detection, especially for 5-day ahead forecasts.

### 2. `Notebook_ML+LSTM--TRIAL2.ipynb`  
- Combines ML models with **LSTM-based deep learning models**.  
- Improves overall metrics compared to pure ML but still has difficulty predicting sharp peaks and extreme values.

### 3. `NotebookDL--FINAL.ipynb`  
- Main notebook implementing the **full hybrid deep learning ensemble**.  

#### Model Architecture
- Integrates multiple deep learning models: **LSTM**, **GRU**, and **CNN**, capturing both sequential and spatial patterns.  
- Each model is trained separately; outputs are combined using an **ensemble strategy**.  
- Ensemble reduces individual model biases and improves generalization, particularly for rare or extreme events.  

![Hybrid Ensemble Architecture](https://i.ibb.co/xqwphQ0n/diagram.png)  
*Figure: Hybrid deep learning ensemble architecture.*

#### Why Hybrid Ensemble?
- Traditional ML struggles with long-term dependencies and non-linearities in hydrological time series.  
- **LSTM/GRU**: Effective for sequential data.  
- **CNN**: Extracts local patterns and features.  
- Combining models improves peak detection and multi-day forecasts.  

#### Results
- The ensemble outperforms all single-model baselines.  
- Robust 5-day ahead forecasts with improved extreme-event prediction.  

---

## ⚙️ Data Collection & Preprocessing

- **Sources**: `Data_raw_inflow.csv` and `Discharge_data_Raw.csv`.  
- **Processing Steps**:
  1. Time alignment & resampling to daily frequency.  
  2. Merge inflow and discharge data; create **lagged** and **rolling-window features**.  
  3. Handle missing values: short gaps forward-filled/interpolated; long gaps excluded.  
  4. Handle outliers: extreme spikes clipped/replaced using local statistics.  
  5. Scaling: Continuous variables scaled (MinMax or Standard scaling).  

### Handling Zeros
- Zeros replaced with **0.1** to avoid:
  - Log(0) or numerical instability.  
  - Division-by-zero errors in metrics.  
- Assumes zeros are small flows or sensor artifacts; adjust strategy if zeros are physically meaningful.  

---

## 🔄 Rolling / Lagged Features

- **5-day rolling window** used to capture recent hydrological trends for 5-day ahead forecasts.  
- Benefits:
  - Captures short-term accumulation and recession behavior.  
  - Smooths high-frequency noise while preserving peaks.  
  - Provides level and trend information for better multi-day forecasts.  

---

## 📊 Evaluation Metrics

| Metric | Formula | Interpretation |
|--------|---------|----------------|
| **MAE** | $\mathrm{MAE} = \frac{1}{n}\sum_{i=1}^{n} |y_i - \hat{y}_i|$ | Average absolute error. Lower is better. |
| **RMSE** | $\mathrm{RMSE} = \sqrt{\frac{1}{n}\sum_{i=1}^n (y_i - \hat{y}_i)^2}$ | Penalizes large errors more. Lower is better. |
| **R²** | $R^2 = 1 - \frac{\sum_i (y_i-\hat{y}_i)^2}{\sum_i (y_i-\bar{y})^2}$ | Fraction of variance explained. 1 = perfect. |
| **NSE** | $\mathrm{NSE} = 1 - \frac{\sum_i (y_i-\hat{y}_i)^2}{\sum_i (y_i-\bar{y})^2}$ | Hydrology-specific. 1 = perfect; 0 = mean; <0 = worse than mean. |
| **KGE** | $\mathrm{KGE} = 1 - \sqrt{(r-1)^2 + (\alpha-1)^2 + (\beta-1)^2}$ | Decomposes correlation, variability, and bias; closer to 1 is better. |

**Notes:**  
- Use MAE and RMSE together: MAE for interpretable average error, RMSE emphasizes large deviations (peaks).  
- NSE and KGE are hydrology-specific and useful for comparing models.

---

## 📈 Final Metrics & Key Inferences

### Final Metrics Table
![Final Metrics Table](https://i.ibb.co/qMwYv6wz/qMwYv6wz.png)

### Performance Graphs
- **Observed vs Predicted Inflow/Discharge**  
![Observed vs Predicted](https://i.ibb.co/FqL2zwxw/FqL2zwxw.png)

- **Error Distribution / Residuals**  
![Error Distribution](https://i.ibb.co/SD2v6LpS/SD2v6LpS.png)

### Key Inferences
- Ensemble reduces **RMSE** and **MAE** compared to single-model baselines.  
- **NSE** and **KGE** near 1 indicate the ensemble captures variability and bias well.  
- Improved performance for **peak events** highlights the ensemble’s advantage in modeling extreme inflow/discharge.  
- Remaining underestimation of peaks suggests potential for further improvement via **extreme-event weighting** or specialized loss functions.
