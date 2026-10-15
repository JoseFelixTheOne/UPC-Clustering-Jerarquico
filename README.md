# Modelo 2: Clustering Jerárquico Aglomerativo (Ward)

**Curso:** Machine Learning (1FIS0300) – TB2  
**Caso de Estudio:** Ministerio de Salud (MINSA) – Perú  
**Dataset:** Certificado de Nacido Vivo (`data/NACIMIENTOS_CNV_LIMPIO.parquet`, SHA-256: `24d734ca...`)  
**Muestra:** 30 000 registros independientes (Semilla `42`)  
**Responsable:** jfelix torres  

---

## 1. Descripción del Modelo

Este repositorio contiene la implementación del **Clustering Jerárquico Aglomerativo** utilizando el criterio de mínima varianza de **Ward** sobre 13 características maternas y obstétricas prenatales. 

### Principios de Arquitectura:
- **Benchmark Fiel:** Ajuste de un único enlace de Ward sobre 30 000 observaciones sin requerir submuestras intermedias ni aproximaciones secundarias.
- **Regla Anti-Fuga de Datos:** Se excluyen estrictamente las variables postparto (`PESO_NACIDO`, `TALLA_NACIDO`, `DUR_EMB_PARTO`) como atributos de agrupamiento.
- **Reproducibilidad Total:** Salidas, métricas ($k \in [3, 6]$) y gráficos renderizados directamente en el notebook.

---

## 2. Estructura del Repositorio

```text
Clustering_Jerarquico/
├── data/
│   └── NACIMIENTOS_CNV_LIMPIO.parquet   # Dataset base (~56 MiB)
├── resultados/
│   ├── dendrograma_ward.png             # Dendrograma truncado con corte k=4
│   ├── tasas_riesgo_clusters.png        # Tasas de bajo peso y prematuridad por cluster
│   ├── heatmap_perfiles_z.png           # Mapa de calor de perfiles estandarizados (Z-Score)
│   ├── metricas_k.csv                   # Comparativa cuantitativa (Silueta y Davies-Bouldin)
│   ├── perfiles_clusters.csv            # Estadísticas clínicas por cluster
│   ├── etiquetas_30k.csv                # Asignación de cluster para las 30 000 observaciones
│   └── manifiesto_ejecucion.json        # Manifiesto técnico con hashes, tiempos y librerías
├── TB2_Clustering_Jerarquico.ipynb      # Notebook ejecutable con todos los outputs integrados
├── requirements.txt                     # Dependencias mínimas
└── README.md
```

---

## 3. Guía de Ejecución

### Opción A: Con `uv` (Recomendado)
```bash
# 1. Crear entorno virtual
uv venv .venv

# 2. Instalar dependencias
uv pip install -r requirements.txt

# 3. Iniciar Jupyter o abrir en VS Code seleccionando el kernel .venv
jupyter notebook TB2_Clustering_Jerarquico.ipynb
```

### Opción B: Con Python estándar
```bash
python3 -m venv .venv
source .venv/bin/activate  # En Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
