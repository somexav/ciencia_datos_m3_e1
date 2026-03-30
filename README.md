# Estudio sobre el Riesgo de Ataques Cardiacos

**Diplomado Ciencias de Datos — Generación 33 | Módulo 3, Primer Examen**
**Fecha:** Marzo 2026 | **Autor:** Xavier

Aplicación de técnicas de reducción de dimensionalidad para visualizar patrones que sugieran qué pacientes tienen mayor probabilidad de sufrir un ataque cardiaco.

---

## Objetivo

Aplicar técnicas de reducción de dimensiones (PCA, MDS y t-SNE) para visualizar patrones en datos de pacientes e identificar aquellos con mayor riesgo cardíaco, como herramienta de cribado inicial para priorizar atención médica.

---

## Dataset

| Archivo | Descripción |
|---|---|
| `HeartAttack.csv` | Dataset principal con 1.319 registros de pacientes |
| `HeartAttack_Dict.csv` | Diccionario de variables |

### Variables

| Feature | Tipo | Descripción |
|---|---|---|
| `age` | int | Edad del paciente (14–103 años) |
| `gender` | int | Género (1 = masculino, 0 = femenino) |
| `impluse` | int | Pulso (bpm) |
| `pressurehight` | int | Presión arterial sistólica (mmHg) |
| `pressurelow` | int | Presión arterial diastólica (mmHg) |
| `glucose` | float | Nivel de glucosa en sangre (mg/dL) |
| `kcm` | float | Creatina Quinasa-MB (CK-MB) — biomarcador de daño cardíaco |
| `troponin` | float | Troponina — biomarcador de daño al corazón |

### Estadísticos Descriptivos

| Variable | Media | Std | Min | Max |
|---|---|---|---|---|
| age | 56.19 | 13.65 | 14 | 103 |
| impluse | 78.34 | 51.63 | 20 | 1111 |
| pressurehight | 127.17 | 26.12 | 42 | 223 |
| pressurelow | 72.27 | 14.03 | 38 | 154 |
| glucose | 146.63 | 74.92 | 35 | 541 |
| kcm | 15.27 | 46.33 | 0.321 | 300 |
| troponin | 0.36 | 1.15 | 0.001 | 10.3 |

---

## Metodología

### 0. Configuración Inicial
Semilla de reproducibilidad: `random_state = 333`.

### 1. Análisis Exploratorio (EDA)
Revisión de distribuciones, estadísticos descriptivos, tipos de datos e identificación de valores atípicos mediante box plots y matriz de correlación.

- **Correlación relevante:** `pressurehight` ↔ `pressurelow` (r = 0.59), fisiológicamente coherente.

### 1.1. Limpieza de Datos
Capping fisiológico (no eliminación) para preservar pacientes de alto riesgo. Se modificaron **6 valores** en total:

| Índice | Variable | Valor original | Valor cappado | Límites |
|---|---|---|---|---|
| 63, 717, 1069 | impluse | 1111 | 200 | 30–200 bpm |
| 708, 1060 | impluse | 20 | 30 | 30–200 bpm |
| 209 | pressurehight | 42 | 60 | 60–300 mmHg |

Troponina, CK-MB y glucosa se conservan sin capping como señales clínicas válidas.

### 1.2. Selección de Features
- **Excluida:** `gender` (variable binaria categórica; distorsionaría la escala en PCA)
- **Incluidas:** `age`, `impluse`, `pressurehight`, `pressurelow`, `glucose`, `kcm`, `troponin` — 7 variables continuas fisiológicas
- Shape resultante: **(1319, 7)**

---

## Técnicas de Reducción de Dimensionalidad

### 2. PCA (Análisis de Componentes Principales)

- Estandarización Z-score (`StandardScaler`) sobre las 7 variables numéricas
- Reducción a 2 componentes principales
  - **PC1**: 22.94% de varianza explicada
  - **PC2**: 15.55% de varianza explicada
  - **Total 2D**: 38.48%
- Principales contribuyentes a PC1: `pressurehight`, `pressurelow`, `troponin`, `kcm`

**Segmentación:** Umbral **PC1 > 2** (valores positivos indican biomarcadores cardíacos elevados)

| Grupo | N | % |
|---|---|---|
| Riesgo bajo | 1.202 | 91.1% |
| **Alto riesgo** | **117** | **8.9%** |

### 3. MDS Métrico (Escalado Multidimensional)

- Preserva distancias euclidianas del espacio original
- Calidad evaluada mediante diagrama de Shepard (stress)
- **Segmentación:** radio = 2.5 (pacientes fuera del círculo = alto riesgo)

| Grupo | N |
|---|---|
| Riesgo bajo | 1.187 |
| **Alto riesgo** | **132** |

### 4. t-SNE

- Reducción no lineal; perplexidad optimizada
- Calidad evaluada con métrica de **trustworthiness**
- **Segmentación:** percentil 90 de distancias al centroide (top 10% = alto riesgo)

---

## Resultados Clave

- **Separación clara en el espacio PCA** a lo largo de PC1.
- **Troponina y CK-MB** son los mejores predictores de riesgo cardíaco en este dataset.
- Edad, presión arterial y glucosa muestran menor poder discriminativo.
- El grupo de alto riesgo presenta biomarcadores consistentes con infarto agudo de miocardio.
- Los tres métodos convergen en identificar aproximadamente el **8–10%** de pacientes como alto riesgo.

---

## Archivos Generados

| Archivo | Contenido |
|---|---|
| `X_mds_m.csv` | Proyección MDS métrico |
| `X_mds_nm.csv` | Proyección MDS no métrico |
| `X_tsne.csv` | Proyección t-SNE |

---

## Notebook

El análisis completo se encuentra en [M3E1_HeartAttack_Xavier.ipynb](M3E1_HeartAttack_Xavier.ipynb).

---

## Tecnologías

- Python 3
- pandas, numpy
- scikit-learn (`PCA`, `StandardScaler`, `MDS`, `TSNE`, `trustworthiness`, `NearestNeighbors`)
- matplotlib, seaborn
- scipy (`procrustes`)

---

## Limitaciones

- No existe variable de diagnóstico confirmado; la validación es indirecta vía biomarcadores.
- PCA asume linealidad; t-SNE y MDS no lineal pueden revelar patrones adicionales.
- El análisis es exploratorio, no un modelo predictivo clínico.
