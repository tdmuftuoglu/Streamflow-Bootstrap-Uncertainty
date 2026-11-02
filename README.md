# Streamflow-Bootstrap-Uncertainty

Python code for the study

> Müftüoğlu, T.D. *Sampling uncertainty in key streamflow statistics as a function of record length: a multi-climate bootstrap study.* 

The code quantifies how the sampling error of streamflow statistics computed from a short record decreases as the record becomes longer. Whole water years are resampled from long daily records to create synthetic records of 5–50 years. Each short-record estimate is compared with the full-record value, and the decay of the error with record length $`n`$ is described by the power law $`\mathrm{RMSE}(n) = \alpha\, n^{-\beta}`$. The results are applied to a catchment with a ten-year record (Çine Çayı, Turkey).

A single script produces all figures, tables and supplementary material of the paper.

## Contents

- [Repository contents](#repository-contents)
- [Analysis steps](#analysis-steps)
- [Data](#data)
- [How to run](#how-to-run)
- [Settings](#settings)
- [Outputs](#outputs)
- [Citation](#citation)
- [Key references for the methods](#key-references-for-the-methods)
- [Contact](#contact)

## Repository contents

| File | Description |
|---|---|
| `streamflow_bootstrap_analysis.ipynb` | Google Colab notebook (one cell) |
| `streamflow_bootstrap_analysis.py` | The same code as a plain Python script |
| `README.md` | This file |

## Analysis steps

1. **Data screening.** Daily GRDC discharge files are read and grouped into water years (October–September, labelled by the end year). The value −999 is treated as missing. A water year is used only if at least 95% of its days are valid. Zero discharge is kept as a valid observation.
2. **Catchment description.** For every catchment the script computes flow statistics and hydrological signatures, examines zero-flow days and tests the annual series. The signatures are:
   - coefficient of variation of annual flow ($`\mathrm{CV}_a`$) and of daily flow
   - baseflow index (Lyne–Hollick filter, $`\alpha = 0.925`$, three passes)
   - zero-flow fraction and $`Q_{90}/\bar{Q}`$
   - flow-duration-curve slope
   - strength and timing of seasonality
   - Richards–Baker flashiness index

   The tests applied to annual mean, annual maximum and annual minimum 7-day flow are:
   - Mann–Kendall and Hamed–Rao modified Mann–Kendall
   - Sen's slope
   - Pettitt change point
   - Brown–Forsythe (median-centred Levene) test
   - lag-1 autocorrelation and Ljung–Box test
3. **Resampling.** Synthetic records of $`n \in \{5, 8, 10, 12, 15, 20, 25, 30, 40, 50\}`$ years are generated with five schemes. Schemes 1–3 use $`B = 1000`$ replicates per $`n`$.

   | Scheme | Label | Method |
   |---|---|---|
   | 1 | SUB | Subsampling without replacement |
   | 2 | IID | Bootstrap of independent years |
   | 3 | CBB | Circular block bootstrap with block lengths $`L = 3, 5, 10`$ years; $`L = 5`$ is the primary scheme |
   | 4 | WIN | All contiguous $`n`$-year windows of the observed record (used when at least ten windows exist) |
   | 5 | SUB-FPC | Subsampling results corrected by the finite-population factor |
4. **Error measures.** Four error measures are computed:
   - relative error $`100\,(\theta_b-\theta_{\mathrm{ref}})/\theta_{\mathrm{ref}}`$
   - log-ratio error $`\ln[(\theta_b+\delta)/(\theta_{\mathrm{ref}}+\delta)]`$ with $`\delta = 0.01\,\bar{Q}`$
   - error normalised by mean flow $`100\,(\theta_b-\theta_{\mathrm{ref}})/\bar{Q}`$
   - absolute error

   They are summarised by RMSE, mean bias with a 95% confidence interval, median, and 5th and 95th percentiles.
5. **Statistics.**
   - mean discharge $`\bar{Q}`$
   - $`Q_{10}`$, $`Q_{50}`$ and $`Q_{90}`$ from the flow-duration curve (Weibull plotting position $`p_i = i/(m+1)`$)
   - CV of daily flow
   - mean annual maximum daily discharge $`\overline{Q}_{\max}`$
   - 100-year flood $`Q_{100}`$ from a Gumbel distribution fitted by L-moments
6. **Power-law fits.** The power law is fitted by non-linear least squares for each catchment, and pooled across catchments with confidence intervals from a catchment bootstrap.
7. **Transferability.** Leave-one-catchment-out cross-validation compares four ways of predicting the RMSE curve of an unseen catchment:
   - M1: pooled mean power law
   - M2: pooled upper power law
   - M3: scaled model $`\mathrm{RMSE}(n) = \kappa \cdot 100\, S\, n^{-\beta}`$, where $`S`$ is the inter-annual coefficient of variation of the statistic in the target catchment; M3b estimates $`S`$ from 10-year windows
   - M4: curve of the most similar donor catchment
8. **Sensitivity analyses.** The analysis is repeated without zero-flow days, after multiplicative detrending, and with block lengths of 3 and 10 years.
9. **Application.** The relations are applied to the short-record catchment set by `TARGET_ID`.

## Data

The discharge data are **not** included in this repository. They are distributed by the Global Runoff Data Centre (GRDC) and can be downloaded free of charge from the GRDC Data Portal (https://portal.grdc.bafg.de). For each station, select **daily data**; the portal delivers one file per station named `<GRDC-No>_Q_Day.Cmd.txt`.

Stations used in the paper:

| No | GRDC No | River | Station | Country | Köppen–Geiger |
|---|---|---|---|---|---|
| 1 | 6196350 | Germasogeia | Phinikaria | Cyprus | Csa |
| 2 | 6196045 | Peristerona | Panagia Bridge | Cyprus | Csa |
| 3 | 6196010 | Xeros | Lazarides | Cyprus | Csa |
| 4 | 6128145 | Argens | Châteauvert | France | Csa |
| 5 | 6128095 | Hérault | Laroque | France | Csa |
| 6 | 6128630 | Réal Martin | La Crau | France | Csa |
| 7 | 6213510 | Alagón | Garcibuey | Spain | Csa |
| 8 | 6212720 | Arlanza | Salas de los Infantes | Spain | Csb |
| 9 | 6227160 | Alfambra | Villalba Alta | Spain | BSk |
| 10 | 6213790 | Badiel | Torre del Burgo | Spain | BSk |
| 11 | 6226460 | Jalón | Jubera | Spain | BSk |
| 12 | 6227620 | Mijares | El Terde | Spain | BSk |
| 13 | 6213800 | Tagus | Trillo | Spain | BSk |
| 14 | 6213540 | Tiétar | Arenas de San Pedro | Spain | BSk |
| 15 | 6212780 | Ucero | Osma | Spain | BSk |
| 16 | 6122135 | Remarde | St-Cyr-sous-Dourdan | France | Cfb |
| 17 | 6128035 | Tech | Amélie-les-Bains | France | Cfa |
| 18 | 6128040 | Têt | Serdinya | France | Cfb |
| 19 | 6226470 | Arba de Luesia | Biota | Spain | Cfa |
| 20 | 6337590 | Sieber | Hattorf | Germany | Dfb |
| 21 | 6444100 | Tisa | Szeged | Hungary | Dfb |
| 22 | 6457850 | Warta | Działoszyn | Poland | Dfb |
| 23 | 6233367 | Segea | Svedala | Sweden | Dfb |
| 24 | 6667500 | Çine Çayı | Kayırlı | Turkey | Csa (short-record catchment) |

The Köppen–Geiger class of each gauge is read from the 1-km present-day map of Beck et al. (2018), which the script downloads from figshare. If the download fails, the climate labels stored in the script are used instead.

Other GRDC stations can be analysed without changing the code. Their names and coordinates are read from the file header.

## How to run

### Google Colab (recommended)

1. Open `streamflow_bootstrap_analysis.ipynb` in Google Colab.
2. Run the cell. A **Choose Files** button appears below it.
3. Select all `*_Q_Day.Cmd.txt` files at once and upload them.
4. When the run finishes, `streamflow_uncertainty_outputs.zip` is downloaded automatically.

With the 24 stations above, a run takes about 15–20 minutes on a standard Colab runtime.

### Local Python

1. Place the GRDC files in a folder.
2. Set `DATA_DIR` in the settings block to that folder.
3. Run `python streamflow_bootstrap_analysis.py`.

The outputs are written to `outputs/` and also packed into `streamflow_uncertainty_outputs.zip`.

### Requirements

Python 3 with:
- numpy, pandas, scipy, matplotlib, statsmodels, requests
- cartopy, pymannkendall, rasterio, adjustText

The last four are installed automatically if they are missing. The script falls back to its own implementation of the Mann–Kendall test if `pymannkendall` is unavailable, and to a plain map if `cartopy` cannot download map data.

## Settings

All analysis choices are collected in the settings block at the top of the script:

| Variable | Default | Meaning |
|---|---|---|
| `SEED` | 2026 | Random seed; results are identical on every run |
| `B` | 1000 | Replicates per scheme and record length |
| `SAMPLE_LENGTHS` | 5, 8, 10, 12, 15, 20, 25, 30, 40, 50 | Synthetic record lengths (years) |
| `WATER_YEAR_START` | 10 | First month of the water year |
| `COMPLETE_FRAC` | 0.95 | Minimum fraction of valid days in a water year |
| `BLOCK_LENGTHS` | 3, 5, 10 | Block lengths of the circular block bootstrap (years) |
| `PRIMARY` | `cbb5` | Primary resampling scheme |
| `MIN_WINDOWS` | 10 | Minimum number of contiguous windows for the WIN scheme |
| `LOG_DELTA_FRAC` | 0.01 | Offset of the log-ratio error as a fraction of mean flow |
| `N_STATION_BOOT` | 1000 | Catchment bootstrap replicates for pooled-fit intervals |
| `N_TARGET_BOOT` | 2000 | Bootstrap replicates for the target-catchment scale parameter |
| `TARGET_ID` | `6667500` | GRDC number of the short-record catchment |
| `DATA_DIR` | `.` | Folder with the GRDC files (local runs only) |

## Outputs

| File | Content |
|---|---|
| `Table1_stations_signatures.csv` | Stations, record lengths, signatures, trend and persistence |
| `Table2_n10_primary.csv` | Errors at $`n = 10`$ years, primary scheme |
| `Table3a_pooled_powerlaw.csv` | Pooled power laws with confidence intervals |
| `Table3b_LOCO_summary.csv` | Leave-one-catchment-out validation |
| `Table4_target_application.csv` | Application to the short-record catchment |
| `Fig1`–`Fig5` (`.png`) | Figures of the paper |
| `supplementary/TableS1`–`S11` (`.csv`) | Supplementary tables (Online Resource 1) |
| `supplementary/FigS1`–`S8` (`.png`) | Supplementary figures (Online Resource 1) |
| `supplementary/replicate_estimates.npz` | All replicate estimates, for re-analysis without resampling again |
| `supplementary/RMSE_curves_all.csv` | RMSE curves for every catchment, statistic, scheme and error measure |
| `supplementary/analytical_curves_mean.csv` | Analytical RMSE of the mean with and without the finite-population correction |
| `run_info.json` | Settings, package versions, run time and station metadata |

## Citation

If you use this code, please cite the paper with its most up to date version.

## Contact

Tevfik Denizhan Müftüoğlu, ORCID [0000-0001-5836-3689](https://orcid.org/0000-0001-5836-3689)
