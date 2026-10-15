# Model 2: Agglomerative Hierarchical Clustering (Ward)

**Course:** Machine Learning (1FIS0300) – TB2  
**Case Study:** Ministry of Health of Peru (MINSA)  
**Dataset:** Live Birth Certificates (`data/NACIMIENTOS_CNV_LIMPIO.parquet`, SHA-256: `24d734ca...`)  
**Sample Size:** 30,000 independent records (Seed: `42`)  
**Author:** jfelix torres  

---

## 1. Overview

This repository provides an end-to-end, reproducible implementation of **Agglomerative Hierarchical Clustering** using the minimum variance criterion (**Ward Linkage**) on 13 prenatal maternal and obstetric features.

### Engineering & Methodological Highlights:
- **Direct Benchmark Parity:** Single Ward linkage matrix computed directly over 30,000 observations without intermediate subsampling or secondary centroid approximations.
- **Strict Anti-Data Leakage Policy:** Postpartum outcomes (`PESO_NACIDO`, `TALLA_NACIDO`, `DUR_EMB_PARTO`) are strictly excluded from clustering features and used solely for extrinsic clinical validation.
- **Full Reproducibility:** Dendrogram, metrics evaluation ($k \in [3, 6]$), risk profiling, and cluster heatmaps are fully rendered and persisted within the notebook.

---

## 2. Repository Structure

```text
Clustering_Jerarquico/
├── data/
│   └── NACIMIENTOS_CNV_LIMPIO.parquet   # Cleaned dataset (~56 MiB)
├── resultados/
│   ├── dendrograma_ward.png             # Truncated dendrogram with k=4 cutoff
│   ├── tasas_riesgo_clusters.png        # Low birth weight and prematurity rates per cluster
│   ├── heatmap_perfiles_z.png           # Standardized Z-Score profile heatmap
│   ├── metricas_k.csv                   # Quantitative comparison (Silhouette & Davies-Bouldin)
│   ├── perfiles_clusters.csv            # Clinical risk statistics per cluster
│   ├── etiquetas_30k.csv                # Cluster assignments for all 30,000 observations
│   └── manifiesto_ejecucion.json        # Execution manifest (SHA-256, timing, environment)
├── TB2_Clustering_Jerarquico.ipynb      # Self-contained notebook with all outputs embedded
├── requirements.txt                     # Pinned project dependencies
└── README.md                            # Project documentation
```

---

## 3. Getting Started

### Prerequisites
- Python 3.10+
- [`uv`](https://github.com/astral-sh/uv) (recommended) or standard `pip`

### Option A: Using `uv` (Recommended)
```bash
# 1. Create a dedicated virtual environment
uv venv .venv

# 2. Install dependencies
uv pip install -r requirements.txt

# 3. Launch Jupyter or open in VS Code (select .venv kernel)
jupyter notebook TB2_Clustering_Jerarquico.ipynb
```

### Option B: Using standard Python
```bash
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook TB2_Clustering_Jerarquico.ipynb
```

---

## 4. Key Clinical Findings ($k = 4$)

* **Cluster 2 (1.70% of sample):** Isolates 100% of multiple gestations (twins/triplets) presenting **62.35% low birth weight (< 2,500 g)** and **60.00% prematurity (< 37 weeks)** — a risk rate ~10× above the national average.
* **Cluster 0 (2.24% of sample):** Identifies geographical and infrastructure vulnerability (deliveries occurring outside healthcare facilities and non-clinical assistance).
* **Execution Performance:** Complete notebook runs end-to-end in **~18 seconds** on consumer hardware.
