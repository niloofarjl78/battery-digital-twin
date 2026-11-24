# Dataset

This project uses four battery cells from the NASA Ames Prognostics Center of Excellence (PCoE) Battery Data Set: B0005, B0006, B0007, and B0018.

The dataset contains lithium-ion battery charge, discharge, and impedance measurements collected during ageing experiments.

## Data Source

**NASA PCoE Battery Data Set:**

https://www.nasa.gov/intelligent-systems-division/discovery-and-systems-health/pcoe/pcoe-data-set-repository/

**Direct dataset archive:**

https://phm-datasets.s3.amazonaws.com/NASA/5.+Battery+Data+Set.zip

## Required Files

The following MATLAB files are used in this project:

- `B0005.mat`
- `B0006.mat`
- `B0007.mat`
- `B0018.mat`

## Dataset Usage

To use the dataset:

1. Download the [NASA Battery Data Set](https://phm-datasets.s3.amazonaws.com/NASA/5.+Battery+Data+Set.zip).
2. Extract the downloaded archive.
3. Locate the four required MATLAB files listed above.
4. Open Notebook 01 in Google Colab.
5. Upload the four `.mat` files when prompted by the notebook.

Notebook 01 processes the experimental data, extracts battery health indicators, and generates a combined dataset.

The processed dataset is exported as `combined_battery_dataset.csv` to the project's Google Drive results directory. Notebook 02 uses this generated dataset to explore the Digital Twin architecture and battery State-of-Health estimation.

**Note:** The original MATLAB files are not included in this GitHub repository because of their size. Users must download them separately from the original dataset source.

## Dataset Citation

Saha, B. and Goebel, K. (2007). *Battery Data Set*. NASA Prognostics Data Repository, NASA Ames Research Center, Moffett Field, CA.
