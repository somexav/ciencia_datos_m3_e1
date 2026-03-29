# Análisis de Riesgo de Infarto con Reducción de Dimensionalidad

**Módulo 3 — Ejercicio 1 | Ciencia de Datos**

Aplicación de técnicas de reducción de dimensionalidad para identificar patrones de riesgo cardíaco en un dataset de pacientes.

---

## Objetivo

Detectar pacientes con mayor probabilidad de sufrir un infarto mediante la visualización de biomarcadores cardíacos elevados usando PCA. El análisis sirve como herramienta de cribado inicial para priorizar atención médica.

---

## Dataset

| Archivo | Descripción |
|---|---|
| `HeartAttack.csv` | Dataset principal con 1.319 registros de pacientes |
| `HeartAttack_Dict.csv` | Diccionario de variables |

### Variables

| Feature | Descripción |
|---|---|
| `age` | Edad del paciente |
| `gender` | Género (1 = masculino, 0 = femenino) |
| `impluse` | Pulso (bpm) |
| `pressurehight` | Presión arterial sistólica (mmHg) |
| `pressurelow` | Presión arterial diastólica (mmHg) |
| `glucose` | Nivel de glucosa en sangre |
| `kcm` | Creatina Quinasa-MB (CK-MB) — biomarcador de daño cardíaco |
| `troponin` | Troponina — biomarcador de daño al corazón |

---

## Metodología

### 1. Análisis Exploratorio (EDA)
Revisión de distribuciones, estadísticos descriptivos e identificación de valores atípicos.

### 2. Limpieza de Datos
Capping fisiológico (no eliminación) para preservar pacientes de alto riesgo:
- Pulso: 30–200 bpm
- Presión sistólica: 60–300 mmHg
- Troponina, CK-MB y glucosa se conservan como señales clínicas válidas

### 3. Reducción de Dimensionalidad con PCA
- Estandarización Z-score (`StandardScaler`) sobre 7 variables numéricas
- Reducción a 2 componentes principales
  - **PC1**: 22.94% de varianza explicada
  - **PC2**: 15.55% de varianza explicada
  - **Total 2D**: 38.48%
- Principales contribuyentes a PC1: `pressurehight`, `pressurelow`, `troponin`, `kcm`

### 4. Segmentación de Riesgo
Umbral **PC1 > 2** para identificar el grupo de alto riesgo:

| Grupo | N | % |
|---|---|---|
| Riesgo bajo/moderado | 1.239 | 93.9% |
| **Alto riesgo** | **80** | **6.1%** |

### 5. Validación Estadística
Prueba Mann-Whitney U para confirmar diferencias significativas (p < 0.05) entre grupos en troponina y CK-MB.

---

## Resultados Clave

- **Separación clara en el espacio PCA**: dos grupos diferenciables a lo largo de PC1.
- **Troponina y CK-MB** son los mejores predictores de riesgo cardíaco en este dataset.
- Edad, presión arterial y glucosa muestran menor poder discriminativo.
- El grupo de alto riesgo presenta biomarcadores consistentes con infarto agudo de miocardio.

---

## Archivos Generados

| Archivo | Contenido |
|---|---|
| `X_mds_m.csv` | Proyección MDS (métrica) |
| `X_mds_nm.csv` | Proyección MDS (no métrica) |
| `X_tsne.csv` | Proyección t-SNE |

---

## Notebook

El análisis completo se encuentra en [M3E1_HeartAttack_Xavier.ipynb](M3E1_HeartAttack_Xavier.ipynb).

---

## Tecnologías

- Python 3
- pandas, numpy
- scikit-learn (PCA, StandardScaler, MDS)
- matplotlib, seaborn
- scipy (Mann-Whitney U)

---

## Limitaciones

- No existe variable de diagnóstico confirmado; la validación es indirecta vía biomarcadores.
- PCA asume linealidad; métodos no lineales (t-SNE, UMAP) podrían revelar patrones adicionales.
- El análisis es exploratorio, no un modelo predictivo clínico.
