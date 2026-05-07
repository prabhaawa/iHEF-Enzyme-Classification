# iHEF: An Interpretable Hierarchical Ensemble Framework Leveraging ESM-2 for Enzyme Classification

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

## Authors

**Prabha Singh**¹, **Sudhakar Tripathi**², **Anand Bihari**³*

¹ Dr. A.P.J. Abdul Kalam Technical University, Lucknow, India  
² Department of Information Technology, Rajkiya Engineering College, Ambedkarnagar, India  
³ Department of Computational Intelligence, VIT Vellore, Tamil Nadu, India  

\*Corresponding author: anand.bihari@vit.ac.in

---

## Overview

**iHEF** is a two-stage cascade ensemble framework for enzyme classification that combines ESM-2 protein language model embeddings with handcrafted biochemical features.

- **Stage 1** — Binary classification: Enzyme vs. Non-Enzyme (XGBoost + CatBoost ensemble)  
- **Stage 2** — Multiclass classification: EC1–EC7 enzyme type (XGBoost + LightGBM + CatBoost ensemble)  
- **Confidence threshold** τ = 0.75 for cascade routing  
- **Integrated accuracy: 90.84%** | Bootstrap 95% CI [89.14%, 92.53%]

### Key Results

| Stage | Accuracy | F1-Score | AUC-ROC |
|-------|----------|----------|---------|
| Stage 1 (Binary) | 98.11% | 98.15% | 0.9951 |
| Stage 2 (Multiclass) | 87.13% | 87.14% | 0.9843 |
| **Integrated Pipeline** | **90.84%** | **90.89%** | — |

---

## Repository Structure

```
iHEF-Enzyme-Classification/
├── CASCADE_pipeline.ipynb        # Main pipeline: training, evaluation, bootstrap CI
├── Data/
│   ├── X_train_stage1_clean.npy  # Training features Stage 1 (4682 × 357)
│   ├── X_test_stage1_clean.npy   # Test features Stage 1 (1004 × 357)
│   ├── X_train_stage2_clean.npy  # Training features Stage 2 (2535 × 357)
│   ├── X_test_stage2_clean.npy   # Test features Stage 2 (544 × 357)
│   ├── y_train_stage1.npy        # Binary labels — training
│   ├── y_test_stage1.npy         # Binary labels — test
│   ├── y_train_stage2.npy        # EC class labels (0–6) — training
│   ├── y_test_stage2.npy         # EC class labels (0–6) — test
│   └── feature_info.json         # Feature names and category metadata
├── model/
│   ├── pca_esm2.pkl              # Fitted PCA for ESM-2 embeddings
│   ├── scaler_stage1_clean.pkl   # Preprocessing scaler for Stage 1
│   ├── scaler_stage2_clean.pkl   # Preprocessing scaler for Stage 2
│   ├── scaler_esm2.pkl           # ESM-2 feature scaler
│   └── scaler_handcrafted.pkl    # Biochemical feature scaler
├── requirements.txt              # Python dependencies
└── README.md
```

---

## Installation

```bash
git clone https://github.com/prabhaawa/iHEF-Enzyme-Classification.git
cd iHEF-Enzyme-Classification
pip install -r requirements.txt
```

---
## Quick Data Load

```python
import numpy as np

# Stage 1 data
X_train_s1 = np.load('Data/X_train_stage1_clean.npy')
X_test_s1  = np.load('Data/X_test_stage1_clean.npy')
y_train_s1 = np.load('Data/y_train_stage1.npy')
y_test_s1  = np.load('Data/y_test_stage1.npy')

# Stage 2 data
X_train_s2 = np.load('Data/X_train_stage2_clean.npy')
X_test_s2  = np.load('Data/X_test_stage2_clean.npy')
y_train_s2 = np.load('Data/y_train_stage2.npy')
y_test_s2  = np.load('Data/y_test_stage2.npy')

print(f"Stage 1 - Train: {X_train_s1.shape}, Test: {X_test_s1.shape}")
print(f"Stage 2 - Train: {X_train_s2.shape}, Test: {X_test_s2.shape}")
```
## Usage

Open `CASCADE_pipeline.ipynb` in Google Colab or Jupyter Notebook and update the data path in the first cell:

```python
DATA_DIR = 'Data/'
MODEL_DIR = 'model/'
```

The notebook runs the complete pipeline:

1. Data splitting — 60% train / 20% validation / 20% test  
2. Preprocessing — VarianceThreshold → MinMaxScaler → SelectKBest(χ²) → RobustScaler → 178 features  
3. Hyperparameter optimization — Optuna TPE with SMOTE inside each CV fold  
4. Ensemble weight selection on validation set  
5. Cascade threshold (τ = 0.75) selection on validation set  
6. Final evaluation on held-out test set (used only once)  
7. Bootstrap confidence intervals (n = 1000)  

---

## Data

Protein sequences sourced from **UniProtKB/Swiss-Prot**.

| Feature Type | Dimension | Description |
|---|---|---|
| ESM-2 embeddings | 256 (after PCA) | Protein language model representations |
| Biochemical features | 104 | Amino acid composition, physicochemical properties, dipeptides |
| **Total (raw)** | **357** | Combined feature vector per protein |
| **After selection** | **178** | Post SelectKBest(χ²) preprocessing |

**Test set class distribution:**  
Non-Enzyme: 460 samples | EC1–EC7: 77–79 samples each (544 total enzymes)

---

## Citation

```bibtex
@article{singh2025ihef,
  title   = {iHEF: An Interpretable Hierarchical Ensemble Framework
             Leveraging ESM-2 for Enzyme Classification},
  author  = {Singh, Prabha and Tripathi, Sudhakar and Bihari, Anand},
  journal = {Journal of Chemical Information and Modeling},
  year    = {2025}
}
```

---

## License

MIT License — see [LICENSE](LICENSE) for details.

## Contact

- Prabha Singh — prabhaawake@gmail.com  
- Anand Bihari (Corresponding) — anand.bihari@vit.ac.in
