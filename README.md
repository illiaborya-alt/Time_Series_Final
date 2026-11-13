# 48-Hour Electricity Generation Forecasting

**Course:** MATH840 | Time Series  
**Author:** Illia Hrynkiv, Kyiv School of Economics  

This repository contains the final project for the MATH840 Time Series course. The objective is to forecast the hourly electricity generation of an energy company's portfolio for the next 48 hours, providing both a point forecast and a 95% prediction interval.

## Repository Contents

* `train.csv`: Historical hourly electricity generation and weather data (~3.8 years).
* `weather_forecast.csv`: Weather forecast for the 48-hour prediction horizon.
* `_project.ipynb`: The main Jupyter Notebook containing the data processing, model training, validation, and submission generation.
* `48-Hour Electricity Generation Forecasting MATH840 Time Series Final Project Illia Hrynkiv.pdf`: Presentation slides summarizing the methodology, failed approaches, and final results.
* `README.md`: This file.

## How to Run the Code (Інструкція зі запуску)

The project is designed to be easily reproducible. The recommended way to run this code is using **Google Colab**, as the notebook contains specific upload/download widgets for this environment.

### Option A: Running in Google Colab (Recommended)
1. Download `train.csv`, `weather_forecast.csv`, and `_project.ipynb` to your local machine.
2. Go to [Google Colab](https://colab.research.google.com/) and upload the `_project.ipynb` notebook (`File` -> `Upload notebook`).
3. Run the first code cell (Setup).
4. Run the second code cell. A **"Choose Files"** button will appear. Click it and select both `train.csv` and `weather_forecast.csv` from your computer to upload them to the Colab environment.
5. Once uploaded, you can simply run all remaining cells (`Runtime` -> `Run all`).
6. The notebook will process the data, train the final ensemble model, plot the forecast, and **automatically download** the valid submission file named `HRYNKIV_project.csv` to your computer.

### Option B: Running Locally (Jupyter Notebook / JupyterLab)
1. Clone this repository or download all files into a single folder.
2. Ensure you have the required libraries installed (`pandas`, `numpy`, `matplotlib`, `scikit-learn`).
3. Open `_project.ipynb` in your local Jupyter environment.
4. *Note:* The Colab-specific upload widget in cell 2 will gracefully fail and instead look for `train.csv` and `weather_forecast.csv` in the same local directory.
5. Run all cells (`Cell` -> `Run All`).
6. The final output file `HRYNKIV_project.csv` will be saved directly in your working directory.

## LLM Usage Declaration

Large Language Models (LLMs) were used during this project strictly as a brainstorming and debugging partner. I used the LLM to propose various modeling ideas and architectures, which I then independently implemented, mathematically evaluated against the Seasonal Naive (SNAIVE) benchmark on my hold-out validation set, and ultimately rejected when they failed to perform.

Specific examples of this iterative process include:

* **Prophet with Multiplicative Seasonality:** The LLM suggested using Prophet in multiplicative mode to account for increasing variance in generation peaks. I tested this, but rejected it because the model failed catastrophically on near-zero nighttime hours, causing the MAPE to explode to >400% and the Winkler score to reach ~14,975.
* **SARIMAX & Deep Learning:** The LLM proposed complex architectures including PyTorch Neural Networks with Pinball Loss and SARIMAX with exogenous weather regressors. After testing, I rejected this ensemble because it yielded an RMSE of 161.4, which was significantly worse than the SNAIVE baseline (120.4).
* **Unconstrained LightGBM:** The LLM suggested using tree-based models. I found that unconstrained LightGBM overfitted on transition-hour noise (dawn/dusk).

By iteratively testing and rejecting these LLM-proposed ideas, I engineered the final architecture myself: a highly regularized LightGBM (`max_depth=5`) ensembled with the SNAIVE baseline to anchor the variance, combined with strict mathematical post-processing of the prediction intervals to prevent Winkler score penalties.