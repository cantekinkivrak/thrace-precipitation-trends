# Data Schema

This document describes the expected file format for precipitation and teleconnection data, so that the analysis pipeline can be reproduced on user-provided datasets.

## Precipitation files

**Format**: CSV with header row.  
**Filename pattern**: `<station_name>_prcp.csv` (e.g. `edirne_prcp.csv`)  
**Period**: 60 years (1965–2024) — one row per year.

### Required columns

| Column     | Type    | Description |
|------------|---------|-------------|
| `year`     | integer | Calendar year (1965–2024) |
| `Jan`      | float   | January precipitation (mm) |
| `Feb`      | float   | February precipitation (mm) |
| `Mar`      | float   | March precipitation (mm) |
| `Apr`      | float   | April precipitation (mm) |
| `May`      | float   | May precipitation (mm) |
| `Jun`      | float   | June precipitation (mm) |
| `Jul`      | float   | July precipitation (mm) |
| `Aug`      | float   | August precipitation (mm) |
| `Sep`      | float   | September precipitation (mm) |
| `Oct`      | float   | October precipitation (mm) |
| `Nov`      | float   | November precipitation (mm) |
| `Dec`      | float   | December precipitation (mm) |
| `Winter`   | float   | December(Y−1) + January(Y) + February(Y) — meteorological winter |
| `Spring`   | float   | March + April + May |
| `Summer`   | float   | June + July + August |
| `Autumn`   | float   | September + October + November |
| `Annual`   | float   | Sum of all 12 months |
| `Wheat`    | float   | Nov(Y−1) + Dec(Y−1) + Jan + Feb + Mar + Apr + May + Jun — winter-wheat crop year |
| `Sunflower`| float   | Apr + May + Jun + Jul + Aug — sunflower vegetative season |

### Notes

- All precipitation values are monthly **totals** in millimeters.
- `Winter` for year Y uses December of year Y−1; for the first year (1965), Winter is computed using whatever Dec data is available (or NaN).
- `Wheat` for year Y uses Nov(Y−1) and Dec(Y−1); the first year's Wheat uses NaN if 1964 data is unavailable.
- Missing values: handled prior to analysis via cubic Hermite interpolation. The 8-station synthetic dataset has no missing values; the real MGM dataset (available via direct request) contains 63 total missing monthly cells filled by the same method.

### Example

```csv
year,Jan,Feb,Mar,Apr,May,Jun,Jul,Aug,Sep,Oct,Nov,Dec,Winter,Spring,Summer,Autumn,Annual,Wheat,Sunflower
1965,45.2,38.1,52.3,41.7,55.8,38.9,15.4,12.8,28.3,67.2,89.4,72.1,...
1966,52.8,42.5,...
...
```

## Teleconnection files

**Format**: Excel (.xlsx)  
**Filename pattern**: `<INDEX>.xlsx` (e.g. `AO.xlsx`, `NAO.xlsx`, `PNA.xlsx`, `El_Nino-La_Nina.xlsx`)

### AO, NAO, PNA — monthly indices

| Column | Description |
|--------|-------------|
| `Year` | Calendar year (must include 1964 for crop-year matching) |
| `Jan`, `Feb`, ..., `Dec` | Monthly index values (dimensionless) |

Source: NOAA Climate Prediction Center  
Period needed: at least 1964–2024.

### ENSO — overlapping 3-month windows

| Column | Description |
|--------|-------------|
| `Year` | Calendar year (must include 1964) |
| `DJF`  | December(Y−1) + January(Y) + February(Y) average |
| `JFM`  | January + February + March |
| `FMA`  | February + March + April |
| `MAM`, `AMJ`, `MJJ`, `JJA`, `JAS`, `ASO`, `SON`, `OND`, `NDJ` | Successive 3-month windows |

Source: NOAA CPC ONI (Oceanic Niño Index).

## Synthetic data note

The CSV/xlsx files in `data/synthetic/` and `data/teleconnection/` reproduce the **statistical structure** of real Thrace stations — monthly mean, standard deviation, lag-1 autocorrelation — but the actual values are random AR(1) realizations and **do not represent observed precipitation at these locations**. Use them to verify that the pipeline runs end-to-end; do not use them for scientific inference.

To reproduce the manuscript's published results, request the real MGM data via the procedure documented in the main README.
