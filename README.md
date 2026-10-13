# PSDMA — ¿Cuántos mundos hay? PCA + K-Means sobre datos de la ONU

Análisis no supervisado de países usando indicadores demográficos, sociales y económicos de la ONU. El proyecto aplica **limpieza de datos, PCA y clustering K-Means** para comprobar si el mundo se puede dividir de forma útil en solo dos grupos (desarrollados vs. en desarrollo) o si la estructura real de los datos es más rica, en línea con el argumento de *Factfulness* (Hans Rosling).

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `Untitled0 (2).ipynb` | Notebook con todo el análisis (carga, limpieza, PCA, clustering e interpretación) |
| `README.md` | Este documento |

## Datos

- **Fuente:** [`UN.csv`](https://raw.githubusercontent.com/manoelgadi/PSDMA/refs/heads/main/datasets/UN.csv) (repositorio `manoelgadi/PSDMA`). El notebook lo descarga directamente por URL, no hace falta tenerlo en local.
- **Unidad de análisis:** país (207 países).
- **Variables principales:**

| Variable | Significado |
|---|---|
| `country`, `region` | Identificadores |
| `tfr` | Tasa global de fecundidad |
| `contraception` | Uso de anticonceptivos |
| `educationMale` / `educationFemale` | Años de educación por sexo |
| `lifeMale` / `lifeFemale` | Esperanza de vida por sexo |
| `infantMortality` | Mortalidad infantil |
| `GDPperCapita` | PIB per cápita |
| `economicActivityMale` / `economicActivityFemale` | Actividad económica por sexo |
| `illiteracyMale` / `illiteracyFemale` | Analfabetismo por sexo |

## Metodología

1. **Carga de datos** con `pandas`.
2. **Valores nulos:** imputación con la **mediana** (más robusta frente a outliers que la media).
3. **Outliers:** tratamiento por **clipping con rango intercuartílico (IQR)**, con límites `Q1 − 1.5·IQR` y `Q3 + 1.5·IQR`, para no perder filas.
4. **Escalado:** `StandardScaler` (media 0, desviación 1), imprescindible para métodos basados en distancias como K-Means.
5. **PCA:** sobre las variables numéricas escaladas; se analizan varianza explicada, scree plot y loadings.
6. **Clustering K-Means** sobre los 5 primeros componentes principales, comparando k = 2 con la solución preferida k = 3 (método del codo y silhouette para k = 2…9).
7. **Interpretación:** perfiles medios por clúster y países representativos.

## Resultados principales

### PCA

- **PC1 explica ≈ 58,5 %** de la varianza y recoge el **desarrollo social y sanitario** (esperanza de vida, anticoncepción vs. fecundidad, mortalidad infantil y analfabetismo).
- **PC2 (≈ 11,5 %)** está dominada por la **actividad económica femenina**.
- Se necesitan **5 componentes para superar el 90 %** de varianza (93,05 % acumulado).
- PCA es útil antes del clustering: reduce colinealidad y ruido.

### Clustering (k = 3)

| Clúster | Perfil | Nº países | TFR | Esp. vida H / M | PIB per cápita | Mortalidad infantil | Analfabetismo fem. |
|---|---|---|---|---|---|---|---|
| 0 | Alto desarrollo (ej. Canadá, Australia, Francia) | 49 | 1,89 | 72,8 / 78,9 | 19 383 | 9,4 | 14,1 |
| 1 | Bajo desarrollo (ej. Afganistán, Angola, Chad) | 60 | 5,47 | 51,2 / 53,9 | 857 | 93,3 | 54,8 |
| 2 | Emergente / transición (ej. Argelia, Argentina, Armenia) | 98 | 3,11 | 67,0 / 72,4 | 2 553 | 29,2 | — |

### Respuestas a las preguntas del proyecto

1. **¿Se puede dividir el mundo en solo dos clústeres?** No de forma satisfactoria: k = 2 es demasiado simplista y fuerza a los países emergentes a los extremos.
2. **¿Cuántos clústeres sugieren los datos?** El codo se dobla entre k = 3 y k = 4; k = 3 es el consenso entre codo y silhouette.
3. **¿k = 2 vs. k = 3?** k = 3 captura mejor la estructura y da fronteras más claras en el plano PC1–PC2.
4. **¿Apoya la evidencia a *Factfulness*?** Sí: el gran tamaño del grupo intermedio (98 países) muestra que la mayoría del mundo está en transición, no en dos bloques.
5. **¿Qué diferencia a los países según PCA?** Principalmente el desarrollo social y sanitario (PC1), y en segundo lugar la participación económica femenina (PC2).

## Limitaciones y posibles mejoras

- **`educationMale` y `educationFemale` quedan constantes** tras imputar con la mediana y aplicar clipping (sus loadings en PCA son ≈ 0), por lo que no aportan información. Convendría revisar su tratamiento (muchos nulos) o excluirlas.
- Imputar con la mediana reduce la varianza real de las variables con muchos nulos; se podría probar KNN-imputer o imputación iterativa.
- Se compararía la robustez con otros algoritmos (clustering jerárquico, GMM) y con otros valores de k.
- Los clústeres se calculan sobre datos con clipping, pero los perfiles se reportan sobre `df_cleaned` (sin clipping); conviene mantener la coherencia al comunicar resultados.

## Cómo ejecutarlo

### Opción 1 — Google Colab
Sube `Untitled0 (2).ipynb` a [Google Colab](https://colab.research.google.com/) y ejecuta todas las celdas (*Entorno de ejecución → Ejecutar todo*).

### Opción 2 — En local

```bash
git clone https://github.com/mdealdamaieu2025-tech/PSDMA.git
cd PSDMA

python -m venv .venv
source .venv/bin/activate        # En Windows: .venv\Scripts\activate

pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook "Untitled0 (2).ipynb"
```

> Se necesita conexión a internet, ya que el dataset se descarga desde GitHub.

## Dependencias

- Python 3.9+
- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- `scikit-learn` (`StandardScaler`, `PCA`, `KMeans`, `silhouette_score`)

## Autoría

Proyecto académico realizado por [@mdealdamaieu2025-tech](https://github.com/mdealdamaieu2025-tech). Dataset y planteamiento: repositorio [`manoelgadi/PSDMA`](https://github.com/manoelgadi/PSDMA).
