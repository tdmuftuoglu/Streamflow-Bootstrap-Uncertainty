This repository provides the reproducible Python/Google Colab code for the analysis of record-length uncertainty in hydrological statistics across diverse climates.

Overview
The codebase implements a bootstrap resampling framework to quantify the error penalty associated with short streamflow records (e.g., 5–15 years). While developed using long-term European reference stations, the methodology is demonstrated on the Çine Çayı (Turkey) application case to estimate the reliability of its 11-year record.

Key Features
Bootstrap Framework: Evaluates uncertainty for six key statistics (Q̅, Q10, Q50, Q90, CV, and Q̅max).

Power-Law Modeling: Estimates RMSE decay as a function of record length.

Climate Analogue Matching: Uses Mediterranean proxies (e.g., Tagus, Spain) to refine uncertainty bounds for similar regions.

Ready for Colab: Designed to run directly in Google Colab with standard GRDC data files.

How to Use
Open the Notebook: Load the .ipynb file in Google Colab.

Upload Data: Provide your daily discharge time series in GRDC format.

Execute: Run the cells to automatically generate uncertainty curves and diagnostic plots.

Citation
Muftuoglu, T. D. (2026). Sampling Uncertainty in Key Streamflow Statistics as a Function of Record Length: A Multi-Climate Bootstrap Study. 
