# eICU Hospital Mortality Cohort — Merge + Visualisations (SQLite + CSV)

This repository contains the code to build a cohort dataset for hospital mortality prediction from the eICU Collaborative Research Database Demo and to generate exploratory cohort visualisations.

The project uses:
- **Demographics** from `patient.csv` (CSV)
- **Hospital mortality labels** from `eicu_v2_0_1.sqlite` (SQLite, `apachepatientresult`)
- **SQLite → pandas** merge using `patientunitstayid`
- **Matplotlib + Seaborn** for visualisations

> Author: Tobi

---

## Repository Structure

- `Pythoncodes_Merge.txt`
  - Loads demographics from `patient.csv`
  - Extracts hospital mortality labels `y` from SQLite
  - Merges into a single dataframe `df`

- `Pythoncodes_Visualisation.txt`
  - Creates cohort plots using dataframe `df`

- `results/`
  - 

---

## Data Used

### Input files
All files are located in a single local folder:

** (`base_path`):**

**Expected filenames:**
- `patient.csv`  
- `eicu_v2_0_1.sqlite`

---

## Target Definition (Label `y`)

The binary label `y` is derived from SQLite table `apachepatientresult`:

- `y = 1` if `actualhospitalmortality = 'EXPIRED'`
- `y = 0` otherwise

The query aggregates at the **patient unit stay level**:

- Uses:
  - `MAX(CASE WHEN actualhospitalmortality = 'EXPIRED' THEN 1 ELSE 0 END)`
- Grouped by:
  - `patientunitstayid`

---

## How the Merge Works

### 1) Load demographics
From `patient.csv` into a pandas dataframe `df_demo`.

### 2) Load labels from SQLite
From `eicu_v2_0_1.sqlite` into a dataframe `df_y`.

### 3) Ensure key type consistency
Both `patientunitstayid` columns are converted to numeric:

- `pd.to_numeric(..., errors="coerce")`

### 4) Merge
Merge is performed as an **inner join** on `patientunitstayid`:

- `df = df_demo.merge(df_y, on="patientunitstayid", how="inner")`

### 5) Diagnostics printed
The merge script prints:
- number of rows after merge
- count of missing `y` values (should be 0 if fully aligned)

**In the finalised build:**
- Merged dataset size: **1838 rows**
- Missing `y` values: **0**
- Mortality rate: **8.43%** (155 deaths out of 1838)


## Visualisations Generated (cohort)

The visualisation script uses the merged dataframe `df` and produces the following figures:

1. **Age distribution (overall)**
2. **Age distribution by hospital mortality (`y`)**
3. **Gender counts**
4. **Hospital mortality rate by gender** (mean of `y` per gender)
5. **Hospital mortality rate by ethnicity** (mean of `y` per ethnicity)

The scripts display figures using `plt.show()`.

> Note: The code provided displays plots but does not automatically export PNGs.  
> Figures should be saved manually from the plot window.



## Saved Outputs (PNG)

Age Distribution by Hospital Mortality.png
Cohort Age distribution.png
Gender Count.png
Hospital Mortality By Gender.png




## How to Run 

### 1) Prerequisites
Install required packages:
# pip install pandas seaborn matplotlib


### 2) Confirm file paths
Update/confirm `base_path` inside:
- `Pythoncodes_Merge.txt`
- `Pythoncodes_Visualisation.txt`

### 3) Build `df`
Run:# python Pythoncodes_Merge.txt


### 4) Run visualisations
Run in the same Python session immediately after the merge:
# python Pythoncodes_Visualisation.txt


### 5) Save figures manually
Because the script uses `plt.show()` and doesn’t call `plt.savefig()`, save each plot manually (File → Save) from the displayed figure window.

---

## Note: Reproducibility Tips

- The visualisation script depends on `df` existing in memory.
  - If you run it as a separate process, you’ll likely get `NameError: df is not defined`.
- Mortality label logic is intentionally simple and binary based on `actualhospitalmortality == 'EXPIRED'`.

---

## Acknowledgements

This work uses the eICU Collaborative Research Database Demo and prepares cohort-level EDA to support later modeling tasks (hospital mortality prediction).

## References 

@article{PhysioNet-eicu-crd-demo-2.0.1,
  author = {Johnson, Alistair and Pollard, Tom and Badawi, Omar and Raffa, Jesse},
  title = {{eICU Collaborative Research Database Demo}},
  journal = {{PhysioNet}},
  year = {2021},
  month = may,
  note = {Version 2.0.1},
  doi = {10.13026/4mxk-na84},
  url = {https://doi.org/10.13026/4mxk-na84}
}

