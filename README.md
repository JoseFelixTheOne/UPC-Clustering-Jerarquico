# Model 2: Agglomerative Hierarchical Clustering (Ward)

**Course:** Machine Learning (1FIS0300) – TB2  
**Case Study:** Ministry of Health of Peru (MINSA)  
**Dataset:** Live Birth Certificates (`data/NACIMIENTOS_CNV_LIMPIO.parquet`, SHA-256: `24d734ca...`)  
**Sample Size:** 30,000 independent records (Seed: `42`)  
**Author:** Felix Torres Terreros  

---

## 1. Overview

This repository provides an end-to-end, reproducible implementation of **Agglomerative Hierarchical Clustering** using the minimum variance criterion (**Ward Linkage**) on 13 maternal and obstetric features. The result is a retrospective segmentation of the study sample, not a prenatal prediction model.

### Engineering & Methodological Highlights:
- **Direct Benchmark Parity:** Single Ward linkage matrix computed directly over 30,000 observations without intermediate subsampling or secondary centroid approximations.
- **Strict Anti-Data Leakage Policy:** Postpartum outcomes (`PESO_NACIDO`, `TALLA_NACIDO`, `DUR_EMB_PARTO`) are strictly excluded from clustering features and used solely for descriptive clinical profiling. Records are not selected using `Bajo_Peso_Nacer` or `Prematuro`.
- **Full Reproducibility:** Dendrogram, metrics evaluation ($k \in [3, 8]$), risk profiling, and cluster heatmaps are fully rendered and persisted within the notebook.

---

## 2. Repository Structure

```text
Clustering_Jerarquico/
├── data/
│   └── NACIMIENTOS_CNV_LIMPIO.parquet   # Cleaned dataset (~56 MiB)
├── resultados/
│   ├── dendrograma_ward.png             # Truncated dendrogram with selected-k cutoff
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

## 4. How to interpret the current results

The notebook evaluates `k=3..8` and uses an explicitly configured `k=7` for the final cut. This is an informed study choice, not a universal or globally optimal value; see `resultados/metricas_k.csv` and `resultados/manifiesto_ejecucion.json` for the exact values.

Clinical rates are calculated only among known outcome values and are rates of this sample, not national estimates. Review `resultados/perfiles_clusters.csv` before writing conclusions. The reported runtime (~15 seconds in the recorded execution) is evidence for that environment only, not a guarantee for every machine.

---

## 5. Method references

- [SciPy `linkage` and Ward criterion](https://docs.scipy.org/doc/scipy/reference/generated/scipy.cluster.hierarchy.linkage.html)
- [scikit-learn `silhouette_score`](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.silhouette_score.html)
- [scikit-learn Silhouette analysis guide](https://scikit-learn.org/stable/auto_examples/cluster/plot_kmeans_silhouette_analysis.html)
