## ERCOT Net-Load Forecasting: FCNN and LSTM Direct/Indirect Methods

This set of codes/data implements our TPEC paper "Net Load Forecasting with Renewable Capacity Features: A Comparison of Direct and Indirect Neural Network Methods".

This repository contains reproducible Jupyter Notebook implementations of four hourly ERCOT net-load forecasting pipelines:

1. FCNN direct forecasting;
2. FCNN indirect forecasting;
3. LSTM direct forecasting;
4. LSTM indirect forecasting.

The indirect methods predict total load, solar generation, and wind generation separately, then calculate:

```text
predicted net load
= predicted total load
- predicted solar generation
- predicted wind generation
```

## Repository structure

```text
Data Download/                 ERCOT 2021-2024 source workbooks and optional plots
Data Preparation/              Canonical timestamp-aligned data preparation
FCNN Direct Method/            FCNN direct model
FCNN Indirect Method/          FCNN component models and indirect result
LSTM Direct Method/            LSTM direct model and optional result plot
LSTM Indirect Method/          LSTM component models and indirect result
Results/                       Paper Fig. 9/12 plotting notebook and APE statistics
fixed_data/                    Canonical model input in CSV/XLSX form
figures/                       Regenerated 600 dpi paper figures
05_Compare_Four_Methods.ipynb  Common-timestamp four-method comparison
requirements.txt               Tested Python dependencies
```

Model artifacts and regenerated predictions are written to `fixed_outputs/`. The directory is created automatically and ignored by Git.

## Environment

Python 3.9 is recommended because the notebooks were developed with TensorFlow 2.10.

```bash
python -m venv .venv
```

Activate the environment, then install the pinned dependencies:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m notebook
```

TensorFlow CPU is sufficient. GPU execution requires a TensorFlow-compatible CUDA/cuDNN installation and may produce small hardware-dependent numerical differences.

## Data

### Official ERCOT download source

The annual source workbooks were obtained from ERCOT's public **Hourly Aggregated Wind and Solar Output** data product:

- [ERCOT Generation page](https://www.ercot.com/gridinfo/generation/index.html) — open **Intermittent Renewable Resources**, then select **Hourly Aggregated Wind and Solar Output**;
- [Hourly Aggregated Wind and Solar Output data-product page](https://www.ercot.com/mp/data-products/data-product-details?id=PG7-126-M) — EMIL ID `PG7-126-M`, Report Type ID `13424`.

ERCOT exposes the available annual files dynamically, so direct year-specific download URLs may change. If that happens, use the data-product page above and select the required year from **Available Files**. These links were verified on August 29, 2026. The exact 2021-2024 workbooks used by this repository are also included in `Data Download/` for reproducibility.

The ready-to-use canonical dataset is included:

- `fixed_data/ercot_2021_2024_hourly.csv` — model input;
- `fixed_data/ercot_2021_2024_hourly.xlsx` — human-readable copy;
- `fixed_data/ercot_2021_2024_audit.json` — row-count and integrity summary.

It contains 35,065 continuous hourly timestamps from `2021-01-01 06:00:00` through `2025-01-01 06:00:00`. Timestamps are UTC-naive keys converted from the ERCOT `America/Chicago` clock so the daylight-saving transition remains unique and continuous.

The model workflows can use the included canonical CSV directly. To regenerate it from the four included ERCOT Wind/Solar workbooks, run:

```text
Data Preparation/01_Prepare_Available_ERCOT_Data.ipynb
```

This preparation notebook reads:

```text
Data Download/ERCOT_2021_Hourly_WindSolar_Output.xlsx
Data Download/ERCOT_2022_Hourly_WindSolar_Output.xlsx
Data Download/ERCOT_2023_Hourly_WindSolar_Output.xlsx
Data Download/ERCOT_2024_Hourly_WindSolar_Output.xlsx
```

Running the preparation notebook rewrites the three files in `fixed_data/`. It validates timestamps, hourly continuity, missing values, nonnegative renewable generation/capacity, one-to-one wind/solar alignment, and the net-load identity.

The forecasting code does not require the legacy NLDAS files. Solar forecasting uses generation/capacity history, local calendar information, and `clear_sky_proxy`; wind forecasting uses generation/capacity history and local calendar information.

## Running order

The direct and indirect branches may be trained independently. Within each indirect branch, run the three component models before the result notebook.

### FCNN direct

```text
FCNN Direct Method/FCNN_net load.ipynb
```

### FCNN indirect

```text
FCNN Indirect Method/1_FCNN_total load.ipynb
FCNN Indirect Method/2_FCNN_solar power.ipynb
FCNN Indirect Method/3_FCNN_wind power.ipynb
FCNN Indirect Method/4_FCNN_indirect result.ipynb
```

### LSTM direct

```text
LSTM Direct Method/LSTM_net load.ipynb
LSTM Direct Method/LSTM Direct Prediction Graph.ipynb  # optional plot
```

### LSTM indirect

```text
LSTM Indirect Method/1_LSTM_total load.ipynb
LSTM Indirect Method/2_LSTM_wind power.ipynb
LSTM Indirect Method/3_LSTM_solar power.ipynb
LSTM Indirect Method/4_LSTM_indirect result.ipynb
```

After all four final prediction files exist, run:

```text
05_Compare_Four_Methods.ipynb
```

The comparison uses only timestamps shared by all four methods and verifies that their actual net-load targets agree before calculating metrics.

To regenerate the paper percentage-error plots and Table IV statistics from the four final prediction files, run:

```text
Results/06_Plot_Percentage_Errors.ipynb
```

## Output contract

Every trained component writes uniquely named artifacts to `fixed_outputs/`:

- a TensorFlow `.keras` model;
- a preprocessing `.joblib` file;
- training history in CSV format;
- predictions in CSV and XLSX formats;
- metrics in JSON format.

Prediction tables use the common columns `time`, `actual`, and `predict`. Indirect result notebooks align all component files by timestamp with one-to-one merge validation rather than positional arithmetic.

## Reproducibility safeguards

- chronological train/validation/test splits;
- preprocessing fitted on training data only;
- sequence-window continuity checks;
- fixed Python, NumPy, and TensorFlow random seeds;
- deterministic TensorFlow operations where supported;
- `shuffle=False` during time-series training;
- nonnegative and installed-capacity constraints for renewable predictions;
- zero-safe percentage metrics;
- common-timestamp comparison across the four final methods.

Notebook outputs already stored in the files are retained as reference results. They are not required for execution; running the notebooks regenerates external artifacts in `fixed_outputs/`.


## Contributions:
Oluwafolajimi Samuel Bolusteve and Linhan Fang developed this set of programs/data. Xingpeng Li supervised this work.


## Citation:
If you use any of our codes/data for your work, please cite the following paper as your reference:

Oluwafolajimi Samuel Bolusteve, Linhan Fang, and Xingpeng Li, “Net Load Forecasting with Renewable Capacity Features: A Comparison of Direct and Indirect Neural Network Methods”, *IEEE Texas Power and Energy Conference*, College Station, TX, USA, Feb. 2027.

Paper website: https://rpglab.github.io/papers/Samuel-Linhan-NetLoad-forecast/


## Contact:
Dr. Xingpeng Li

University of Houston

Email: xli83@central.uh.edu

Website: https://rpglab.github.io/


## License:
This work is licensed under the terms of the <a class="off" href="https://creativecommons.org/licenses/by/4.0/"  target="_blank">Creative Commons Attribution 4.0 (CC BY 4.0) license.</a>


## Disclaimer:
The author doesn’t make any warranty for the accuracy, completeness, or usefulness of any information disclosed; and the author assumes no liability or responsibility for any errors or omissions for the information (data/code/results etc) disclosed.
