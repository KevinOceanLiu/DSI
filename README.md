# R Codes for Degree of spatial interpretability
### Author
Haiyang LIU; Yongze SONG; Wen YI
### Date
2026/01/22
### Reference:
Liu, H., Song, Y., & Yi, W. (2026). Degree of spatial interpretability. International Journal of Geographical Information Science, 1–21. https://doi.org/10.1080/13658816.2026.2614335
---
### Demo dataset

The demo dataset is derived from the Australia case study in this paper and is consistent with the input data used to compute the DSI metrics in the manuscript. It covers the Australian continent at a 0.5° × 0.5° grid (sampling-point) resolution; each row represents one grid cell/point and includes geographic coordinates (WGS84), the response variable `y` (richness in the paper), and the same set of environmental, topographic, and soil predictors (X) as reported in the study. The `residual` column was generated from the paper’s linear regression model (lm) by predicting on the held-out test set after the train/test split and computing residuals as `y − ŷ`. The file provided in this repository is the standardized test-set input (lon/lat/y/residual/all X) intended to reproduce the paper’s Moran’s I and Q value calculations, and thus derive θmin, θprobable, and θmax.

---

### How to run: `DSI calculation code.R`

### 1) Prepare your data (CSV)

Your CSV must include these columns (recommended to use exactly these names):

- `lon`: longitude  
- `lat`: latitude  
- `y`: dependent variable  
- `residual`: a single column of model residuals (computed by you)  

All other columns will be treated as X variables (used for Q-value stratification).

### 2) Edit the 4 parameters at the top of the script

- `infile`: path to your CSV file  
- `projected_crs`: a projected CRS in meters  
  - Australia: `3577`  
  - Other regions: use an appropriate local UTM zone or a national equal-area projection (must be in meters)  
- `k_neighbors`: KNN neighbor number for Moran’s I  
  - Dense samples: `6–10`  
  - Sparse samples: `10–20`  
- `alternative`: hypothesis for Moran’s I test  
  - Common choice: `"greater"` (tests for positive spatial autocorrelation)

### 3) What you get after running

The script prints 7 metrics:

- `δ0a`: Moran’s I of `y`  
- `δra`: Moran’s I of `residual`  
- `δ0h`: Q value of `y` (via GD)  
- `δrh`: Q value of `residual` (via GD)  
- `θmin = min(ηa, ηh)`  
- `θprobable = max(ηa, ηh)`  
- `θmax = 1 − (1−ηa)(1−ηh)`

### 4) Common issues and fixes

- **Error: “not enough points for KNN”**  
  You have too few samples or `k_neighbors` is too large. Reduce `k_neighbors`.

- **Unreasonable results due to CRS**  
  Make sure `projected_crs` is a projected coordinate system in meters. Do not use `EPSG:4326` (degrees) to build distance-based neighbors.
