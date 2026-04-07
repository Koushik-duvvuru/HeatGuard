# HeatGuard AI — Setup Guide

## 📁 File Structure
```
your_project/
├── app.py                          ← Streamlit dashboard
├── data_preprocessing.py           ← 3-class label engineering + feature pipeline
├── neural_network.py               ← Custom NN built from scratch (NumPy only)
├── train_models.py                 ← Training pipeline for all 5 models
├── InfantSmartWear_TemperatureMonitoring_v1.csv
├── wearable_sensor_data.csv
├── Final_Dataframe_P1.csv
├── Final_Dataframe_P2.csv
├── saved_models/                   ← auto-created after training
└── plots/                          ← auto-created after training
```

## ⚙️ Install Dependencies

```bash
pip install -r requirements.txt
```

## 🚀 Step 1 — Train Models

```bash
python train_models.py
```

This will:
- Load and merge all 4 datasets (~7,000 rows combined)
- Engineer 17 features + 3-class labels (Normal / Moderate / High)
- Balance classes via oversampling
- Train: Custom NN, Random Forest, Gradient Boosting, SVM, Logistic Regression
- Save `.pkl` models + `metrics.json` to `saved_models/`
- Save confusion matrices, ROC curves, comparison charts to `plots/`

## 🌐 Step 2 — Run Dashboard

```bash
streamlit run app.py
```

## 📲 Step 3 — Configure Twilio WhatsApp

Refer the `.env.example` file.

Alerts fire automatically on **Moderate** or **High** predictions.

## 🎯 3-Class Label Logic

### Heat Stress Score
| Condition | Points |
|-----------|--------|
| Body temp ≥ 39.5 °C | +4 |
| Body temp ≥ 38.5 °C | +3 |
| Body temp ≥ 37.8 °C | +1 |
| Ambient ≥ 38 °C | +3 |
| Ambient ≥ 32 °C | +2 |
| Ambient ≥ 28 °C | +1 |
| Humidity ≥ 80% | +2 |
| HR ≥ 160 bpm | +3 |
| HR ≥ 130 bpm | +2 |

**Score ≥ 7 → High | 3–6 → Moderate | 0–2 → Normal**

### Dehydration Score
| Condition | Points |
|-----------|--------|
| Skin resistance ≥ 350 Ω | +4 |
| Skin resistance ≥ 250 Ω | +3 |
| Body temp ≥ 39.0 °C | +3 |
| HR ≥ 160 bpm | +3 |
| Humidity < 30% | +2 |

**Score ≥ 7 → High | 3–6 → Moderate | 0–2 → Normal**

## 🧠 Custom Neural Network Architecture

```
Input (17 features)
  ↓
Dense(128) → BatchNorm → ReLU → Dropout(30%)
  ↓
Dense(64)  → BatchNorm → ReLU → Dropout(20%)
  ↓
Dense(32)  → BatchNorm → ReLU
  ↓
Dense(3)   → Softmax
```

- Optimiser: **Adam** with learning rate decay
- Regularisation: **L2** weight penalty
- Training: **Early stopping** (patience=20, val_loss)
- Initialisation: **He initialisation**
- All implemented from scratch using **NumPy only**
