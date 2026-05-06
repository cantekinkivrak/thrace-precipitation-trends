# Hidden Precipitation Trends in the Thrace Region of Turkey

This repository contains the analysis code, derived results, and synthetic test data accompanying the manuscript:

> **Hidden Precipitation Trends in the Thrace Region of Turkey: A Multi-Method Analysis with Empirical Permutation Testing**  
> Cantekin Kıvrak (TAGEM, Atatürk Soil, Water and Agricultural Meteorology Research Institute, Kırklareli) and Mehmet Şener (Namık Kemal University, Tekirdağ).  
> Submitted to *Theoretical and Applied Climatology*.

## Repository contents

```
.
├── README.md                          # This file
├── LICENSE                            # MIT License (code) + CC-BY-4.0 (results/figures)
├── data/
│   ├── data_schema.md                 # Documentation of expected file format
│   ├── synthetic/                     # 8 synthetic precipitation CSVs (60 years × 19 vars)
│   └── teleconnection/                # 4 synthetic teleconnection xlsx files (1964–2024)
├── notebooks/
│   ├── permutation_tests.ipynb        # ITA/ZITA permutation calibration + MIC analysis
│   ├── ipta_permutation.ipynb         # IPTA monthly D_m permutation testing
│   └── wheat_sunflower_mic_correction.ipynb  # Crop-calendar matching for MIC
├── results/                           # Analysis outputs (computed on real MGM data)
│   ├── zita_validation_results.csv    # ZITA σ_D permutation calibration (608 cells)
│   ├── ipta_permutation_results.csv   # IPTA monthly permutation (96 cells)
│   ├── mic_permutation_pvalues.csv    # MIC permutation (608 combinations)
│   ├── mic_wheat_sunflower_corrected.csv  # Crop-calendar-corrected MIC (64 combinations)
│   ├── table5_ipta_with_perm.csv      # Manuscript Table 5 data (with stars)
│   └── table6_mic_summary.csv         # Manuscript Table 6 summary
├── figures/                           # Key figures from the manuscript
│   ├── Figure_2_methodology_flowchart.png
│   └── Figure_8_mic_teleconnection.png
└── docs/
    ├── citation.bib                   # BibTeX citation for the paper
    └── methodology_summary.md         # Brief methodological overview
```

## ⚠️ Data availability statement

**Raw monthly precipitation data** for the eight Thrace stations (Edirne, Kırklareli, Lüleburgaz, Uzunköprü, İpsala, Çorlu, Tekirdağ, Sarıyer) were obtained from the **Turkish State Meteorological Service (Meteoroloji Genel Müdürlüğü, MGM)** under their data-sharing policy and **cannot be redistributed in this repository**. 

Researchers wishing to reproduce the analysis on real data should request access directly from MGM via [https://www.mgm.gov.tr](https://www.mgm.gov.tr) following their institutional data-access procedures.

To enable code testing and pipeline verification without the original data, this repository includes **synthetic precipitation series** that match the statistical structure (mean, variance, autocorrelation) of the real stations but contain no actual MGM measurements. See `data/synthetic/` and `data/data_schema.md` for details.

**Teleconnection indices** (AO, NAO, PNA, ENSO) used in the analysis are publicly available from the NOAA Climate Prediction Center ([https://www.cpc.ncep.noaa.gov/](https://www.cpc.ncep.noaa.gov/)). The synthetic xlsx files in `data/teleconnection/` are also provided for testing; for actual reproduction, download the latest indices directly from NOAA CPC.

**Derived analysis results** (CSV files in `results/`) ARE the outputs computed on the real MGM data and are shared openly — these contain trend test statistics, permutation p-values, and significance counts that constitute the manuscript's empirical findings.

## Getting started

### Requirements

- Python ≥ 3.10 (tested on 3.12)
- numpy, pandas, scipy, matplotlib, openpyxl, tqdm

```bash
pip install numpy pandas scipy matplotlib openpyxl tqdm
```

### Reproducing the analysis with synthetic data

The notebooks are designed for Google Colab (with file uploads), but can be run locally:

1. Open `notebooks/permutation_tests.ipynb` in Jupyter or Colab
2. When prompted to upload files, use the synthetic CSVs from `data/synthetic/`
3. The notebook will produce `mic_permutation_pvalues.csv` and related results

Note: synthetic-data results will **not match** the manuscript's published values (which are based on real MGM data). The synthetic pipeline is provided to verify that the **code runs end-to-end without errors** and produces output of the expected schema.

### Verifying manuscript values on real data

If you have obtained the real MGM data through their access procedure, place the CSVs (formatted per `data/data_schema.md`) in a folder, modify the file paths in the notebooks, and re-run. The seed-fixed permutation will reproduce the manuscript's exact significance counts and p-values.

## Method summary

The pipeline consists of seven stages:

1. **Homogeneity testing** (SNHT, Buishand, Pettitt, Von Neumann) → 152 series → 149 retained, 3 suspect
2. **Serial correlation** (Anderson test) → 136 independent, 13 autocorrelated (use MMKH)
3. **MK / MMKH + Sen's slope** → 16 significant trends (14↓, 2↑)
4. **ITA Low/Medium/High decomposition** with **empirical year-permutation testing** → 9 overall significant + 31 hidden trends
5. **IPTA polygon analysis** with permutation testing → September +16.7 mm (5/8 stations sig), November −15.2 mm (2/8 sig)
6. **CUSUM change-point** detection → 3 significant change-points (Uzunköprü Nov 1999, İpsala Annual 1981, Tekirdağ Nov 2002)
7. **MIC teleconnection** analysis with permutation testing → AO and NAO co-dominant; 11 candidate nonlinear associations

See `docs/methodology_summary.md` for a more detailed walkthrough.

## Key methodological contribution

We empirically calibrated the analytical Şen-Onyutha σ_D significance formula used in ITA via 1,000-permutation Monte Carlo testing. The analytical formula was found to be **anti-conservative** by a median factor of 16.5 (overall ITA) and 2.8 (IPTA monthly D_m). Empirical permutation p-values are reported throughout the manuscript and supersede the analytical formula values.

## Citation

If you use this code or methodology, please cite:

```bibtex
@article{Kivrak2026Thrace,
  author = {K{\i}vrak, Cantekin and {\c S}ener, Mehmet},
  title  = {Hidden Precipitation Trends in the Thrace Region of Turkey: A Multi-Method Analysis with Empirical Permutation Testing},
  journal = {Theoretical and Applied Climatology},
  year = {2026},
  note = {Submitted}
}
```

## License

- **Code** (notebooks, Python scripts): [MIT License](LICENSE)
- **Derived results** (CSVs in `results/`) and **figures**: [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
- **Synthetic data files** (CSVs in `data/synthetic/`, xlsx in `data/teleconnection/`): [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
- **Real precipitation data**: not contained in this repository; subject to MGM data-sharing policy

## Contact

Cantekin Kıvrak — Head, Agrometeorology and Climate Change Department  
Atatürk Soil, Water and Agricultural Meteorology Research Institute (TAGEM)  
Kırklareli, Turkey  
[GitHub](https://github.com/cantekinkivrak) · [ORCID](https://orcid.org/0000-0002-6221-2469)

## Acknowledgments

This work extends the doctoral research of CK conducted at Namık Kemal University, Department of Biosystems Engineering, Tekirdağ, Turkey. We thank MGM for providing the precipitation data under their data-sharing policy and the NOAA Climate Prediction Center for maintaining publicly accessible teleconnection index databases.
