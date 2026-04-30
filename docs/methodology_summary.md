# Methodology Summary

A condensed methodological reference for users of this repository. For full details, see the manuscript Sections 2.3.1–2.3.7 and 2.4.

## Pipeline overview

```
152 series (8 stations × 19 variables)
    │
    ├──── Stage 1: Homogeneity testing (SNHT, Buishand, Pettitt, Von Neumann)
    │     → 141 useful + 8 doubtful + 3 suspect (all August precipitation)
    │
    ├──── Stage 2: Serial correlation (Anderson 2-tailed test)
    │     → 136 independent + 13 autocorrelated (use MMKH-pre-whitening)
    │
    ├──── Stage 3: MK / MMKH + Sen's slope estimator
    │     → 16 significant trends (14 decreasing, 2 increasing)
    │
    ├──── Stage 4: ITA Low/Medium/High decomposition + Z_ITA significance
    │     │   ⚡ KEY STEP: σ_D analytical formula (Eq. 10) found anti-conservative
    │     │              empirical 1,000-permutation calibration applied
    │     │              empirical/analytical σ_D ratio: median 16.5 (overall)
    │     │
    │     → Empirical permutation: 9 overall significant + 31 hidden trends
    │     → Hidden = overall NS but ≥1 sub-category significant
    │
    ├──── Stage 5: IPTA polygon analysis with Shoelace formula + permutation
    │     → September: mean +16.7 mm, sig at 5/8 stations (α≤0.10)
    │     → November: mean −15.2 mm, sig at 2/8 stations
    │     → February (1/8 sig: Sarıyer), July (1/8 sig: Edirne)
    │
    ├──── Stage 6: CUSUM change-point (distribution-free)
    │     → Applied to 16 MK/MMKH-significant series
    │     → 3 statistically significant change-points:
    │         • Uzunköprü November 1999 (p ≤ 0.05)
    │         • İpsala Annual 1981 (p ≤ 0.05)
    │         • Tekirdağ November 2002 (p ≤ 0.10)
    │
    └──── Stage 7: MIC teleconnection analysis (Maximal Information Coefficient)
          → 8 stations × 19 variables × 4 indices = 608 combinations
          → Permutation-tested significance:
              AO  : 36 sig at p≤0.10, 22 at p≤0.05
              NAO : 31 sig at p≤0.10, 21 at p≤0.05
              PNA : 8 sig at p≤0.10, 4 at p≤0.05
              ENSO: 10 sig at p≤0.10, 4 at p≤0.05
          → 11 candidate nonlinear associations (MIC perm sig + Pearson NS)
```

## Variable–index temporal matching for MIC

Each precipitation variable is matched against the corresponding teleconnection index using a **window-aware aggregation** to respect the climatological context:

| Variable     | Months matched           | Year offset      |
|--------------|--------------------------|------------------|
| Jan–Dec (12 monthly) | Same calendar month | Y                |
| Winter       | Dec, Jan, Feb            | Dec(Y−1), Jan/Feb(Y) (DJF) |
| Spring       | Mar, Apr, May            | Y                |
| Summer       | Jun, Jul, Aug            | Y                |
| Autumn       | Sep, Oct, Nov            | Y                |
| Annual       | Mean of all 12 months    | Y                |
| Wheat        | Nov, Dec, Jan, Feb, Mar, Apr, May, Jun | Nov–Dec(Y−1), Jan–Jun(Y) |
| Sunflower    | Apr, May, Jun, Jul, Aug  | Y                |

**Important**: Wheat uses an 8-month crop-year window starting in November (not October). Sunflower uses a 5-month vegetative window ending in August (not September). These match the actual crop calendar in Thrace where winter wheat is planted Nov, harvested late Jun, and sunflower is planted Apr–May, harvested mid-Sep.

## Permutation testing protocol

For each (station, variable, index) combination:
1. Compute the observed test statistic (Z_ITA D-value, IPTA D_m, or MIC)
2. Generate 1,000 permutations by **year-shuffling the precipitation series** (preserving the index series)
3. Recompute the test statistic for each permutation
4. Empirical p-value = (1 + #{|stat_perm| ≥ |stat_obs|}) / (1 + N_perm)

Random seed: `20260430` (used throughout for reproducibility).

## MIC implementation note

MIC values reported in the manuscript Table 6 are computed using the `minepy` library (Reshef et al. 2011). For environments where `minepy` does not compile (e.g., some Colab Python 3.12 setups), a pure-Python equipartition-based fallback is included in the notebooks. The fallback produces MIC values within ~5–15% of `minepy` but uses the **same statistic** for both observed and permuted values, so empirical p-values remain valid.

## Software stack

```
python  ≥ 3.10
numpy   ≥ 1.26
pandas  ≥ 2.0
scipy   ≥ 1.12
matplotlib ≥ 3.8
openpyxl ≥ 3.1
minepy  ≥ 1.2.6  (optional; pure-Python fallback used otherwise)
```

## Reproducibility check

To verify your environment can reproduce the manuscript's findings on real data:

1. Place real CSVs in `data/real/` (matching the schema in `data_schema.md`)
2. Edit notebook cell 4 to point to `data/real/` instead of `data/synthetic/`
3. Run all cells
4. The output `results/mic_permutation_pvalues.csv` should match the version in this repository to within the 1,000-permutation Monte Carlo uncertainty (typically ±0.005 in MIC, ±0.02 in p-value).

If using the synthetic data, results will differ — synthetic series are statistically similar but not identical to real observations.
