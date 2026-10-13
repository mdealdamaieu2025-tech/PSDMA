# PSDMA — How Many Worlds Are There? PCA + K-Means on UN Country Data

Unsupervised analysis of countries using demographic, social and economic indicators from the United Nations. The project applies **data cleaning, PCA and K-Means clustering** to test whether the world can be meaningfully split into just two groups (developed vs. developing) or whether the real structure of the data is richer, in line with the argument in *Factfulness* (Hans Rosling).

## Repository contents

| File | Description |
|---|---|
| `Untitled0 (2).ipynb` | Notebook with the full analysis (loading, cleaning, PCA, clustering and interpretation) |
| `README.md` | This document |

## Data

- **Source:** [`UN.csv`](https://raw.githubusercontent.com/manoelgadi/PSDMA/refs/heads/main/datasets/UN.csv) (repository `manoelgadi/PSDMA`). The notebook downloads it directly from the URL, so no local copy is needed.
- **Unit of analysis:** country (207 countries).
- **Main variables:**

| Variable | Meaning |
|---|---|
| `country`, `region` | Identifiers |
| `tfr` | Total fertility rate |
| `contraception` | Contraceptive use |
| `educationMale` / `educationFemale` | Years of education by sex |
| `lifeMale` / `lifeFemale` | Life expectancy by sex |
| `infantMortality` | Infant mortality |
| `GDPperCapita` | GDP per capita |
| `economicActivityMale` / `economicActivityFemale` | Economic activity by sex |
| `illiteracyMale` / `illiteracyFemale` | Illiteracy rate by sex |

## Methodology

1. **Data loading** with `pandas`.
2. **Missing values:** **median** imputation (more robust to outliers than the mean).
3. **Outliers:** **IQR clipping**, with bounds `Q1 − 1.5·IQR` and `Q3 + 1.5·IQR`, so no rows are lost.
4. **Scaling:** `StandardScaler` (mean 0, standard deviation 1), essential for distance-based methods such as K-Means.
5. **PCA:** on the scaled numeric variables; explained variance, scree plot and loadings are analysed.
6. **K-Means clustering** on the first 5 principal components, comparing k = 2 with the preferred k = 3 (elbow method and silhouette for k = 2…9).
7. **Interpretation:** average profile per cluster and representative countries.

## Key results

### PCA

- **PC1 explains ≈ 58.5%** of the variance and captures **social and health development** (life expectancy, contraception vs. fertility, infant mortality and illiteracy).
- **PC2 (≈ 11.5%)** is dominated by **female economic activity**.
- **5 components are needed to exceed 90%** of the variance (93.05% cumulative).
- PCA is useful before clustering: it reduces collinearity and noise.

### Clustering (k = 3)

| Cluster | Profile | Countries | TFR | Life exp. M / F | GDP per capita | Infant mortality | Female illiteracy |
|---|---|---|---|---|---|---|---|
| 0 | High development (e.g. Canada, Australia, France) | 49 | 1.89 | 72.8 / 78.9 | 19,383 | 9.4 | 14.1 |
| 1 | Low development (e.g. Afghanistan, Angola, Chad) | 60 | 5.47 | 51.2 / 53.9 | 857 | 93.3 | 54.8 |
| 2 | Emerging / transition (e.g. Algeria, Argentina, Armenia) | 98 | 3.11 | 67.0 / 72.4 | 2,553 | 29.2 | — |

### Answers to the project questions

1. **Can the world be meaningfully divided into only two clusters?** Not satisfactorily: k = 2 is too simplistic and forces emerging countries into the two extremes.
2. **How many clusters does the data suggest?** The elbow bends between k = 3 and k = 4; k = 3 is the consensus between the elbow and silhouette.
3. **k = 2 vs. k = 3?** k = 3 captures the structure better and gives clearer boundaries in the PC1–PC2 plane.
4. **Does the evidence support *Factfulness*?** Yes: the large size of the middle group (98 countries) shows most of the world is in transition, not split into two blocs.
5. **What does PCA say about what differentiates countries?** Mainly social and health development (PC1), and secondarily female economic participation (PC2).

## Limitations and possible improvements

- **`educationMale` and `educationFemale` become constant** after median imputation and clipping (their PCA loadings are ≈ 0), so they add no information. Their treatment (many missing values) should be reviewed, or they should be excluded.
- Median imputation reduces the real variance of variables with many missing values; KNN or iterative imputation could be tried.
- Robustness could be compared against other algorithms (hierarchical clustering, GMM) and other values of k.
- Clusters are fitted on clipped data, but profiles are reported on `df_cleaned` (unclipped); keep this consistent when communicating results.

## How to run

### Option 1 — Google Colab
Upload `Untitled0 (2).ipynb` to [Google Colab](https://colab.research.google.com/) and run all cells (*Runtime → Run all*).

### Option 2 — Locally

```bash
git clone https://github.com/mdealdamaieu2025-tech/PSDMA.git
cd PSDMA

python -m venv .venv
source .venv/bin/activate        # On Windows: .venv\Scripts\activate

pip install pandas numpy matplotlib seaborn scikit-learn jupyter
jupyter notebook "Untitled0 (2).ipynb"
```

> An internet connection is required, since the dataset is downloaded from GitHub.

## Dependencies

- Python 3.9+
- `pandas`, `numpy`
- `matplotlib`, `seaborn`
- `scikit-learn` (`StandardScaler`, `PCA`, `KMeans`, `silhouette_score`)

## Authors

Academic project by [@mdealdamaieu2025-tech](https://github.com/mdealdamaieu2025-tech). Dataset and project framing: repository [`manoelgadi/PSDMA`](https://github.com/manoelgadi/PSDMA).
