# 🌽 OnFarm-Nitrogen-Trial-Analytics

## Methods for Analyzing On-Farm Nitrogen Trials to Support Precision Management

This repository contains the analytical scripts developed for the study:

> **Methods for analyzing on-farm nitrogen trials to support precision management**

The repository provides a reproducible analytical workflow for evaluating spatially referenced **on-farm nitrogen (N) experiments** and supporting **precision nitrogen management (PNM)**.

The framework integrates **whole-field, block-based, and grid-based analyses** with machine learning, causal machine learning, agronomic yield-N response modeling, economic optimum nitrogen rate (**EONR**) estimation, and agronomic and economic evaluation of alternative N-management strategies.

---

## 🎯 Study Objectives
The analytical framework was developed to:
1. Compare analytical approaches across spatial scales and on-farm experimental designs.
2. Develop a knowledge-guided stacking machine-learning approach for robust local EONR estimation.
3. Compare stacking machine learning with individual machine-learning algorithms and causal machine learning.
4. Estimate spatially varying agronomic optimum nitrogen rates (**AONR**) and economic optimum nitrogen rates (**EONR**).
5. Quantify the potential, realized benefit, and remaining opportunity associated with precision nitrogen management.

---

## 🔬 Analytical Workflow

The repository follows the major analytical steps used in the study:

```text
On-farm experimental data
        │
        ▼
01_data_preprocessing
        │
        ▼
02_machine_learning
        │
        ▼
03_N_response
        │
        ▼
04_multiscale_analysis
        │
        ▼
05_PNM_evaluation
        │
        ▼
06_statistical_analysis
```

---

# 📂 Repository Structure

```text
OnFarm-Nitrogen-Trial-Analytics/
│
├── README.md
│
├── 01_data_preprocessing/
│   └── Data cleaning, spatial extraction, aggregation,
│       and feature preparation
│
├── 02_machine_learning/
│   └── Individual ML models, stacking ensemble,
│       causal machine learning, and model evaluation
│
├── 03_N_response/
│   └── Yield–N simulation, agronomic response fitting,
│       AONR estimation, and EONR estimation
│
├── 04_multiscale_analysis/
│   └── Whole-field, block-based, and grid-based analyses
│
├── 05_PNM_evaluation/
│   └── Agronomic and economic evaluation of
│       precision nitrogen management scenarios
│
├── 06_statistical_analysis/
│   └── Statistical comparisons, performance metrics,
│       uncertainty analysis, and spatial agreement
│
└── 07_figures/
    └── graphical material
```

---

# 📊 1. Data Preprocessing

The scripts in [`01_data_preprocessing`](01_data_preprocessing/) prepare spatially referenced experimental and environmental variables for subsequent analyses.

The workflow includes processing and integration of:

### Management Variables
* Total N rate
* Preplant N rate
* Sidedress N rate
* Ratio of sidedress N to total N
* Seeding rate
* Treatment information
* Block and grid identifiers

### Yield Data
Corn grain yield was collected using combine-mounted yield monitors.
The preprocessing workflow includes:
* Grain-flow delay correction
* Moisture filtering
* Harvest-speed filtering
* Yield outlier removal
* Grain-moisture standardization
* Assignment of yield observations to experimental grids and blocks

### Soil Variables
Soil properties include:
* Sand
* Silt
* Clay
* Soil organic matter
* Bulk density
* Soil pH
* Saturated soil water content

### Topographic Variables
LiDAR-derived terrain attributes include:
* Relative elevation
* Standardized topographic position index
* Topographic wetness index
* Slope
* Plan curvature
* Profile curvature
* Tangential curvature
* Surface roughness
* Terrain ruggedness index
* Aspect northness
* Aspect eastness

### Remote-Sensing Information

Satellite-derived variables used in the analysis include soil and crop spectral information such as the **soil brightness index (SBI)** and vegetation information used to support precision N management.

---

# 🤖 2. Machine Learning

The [`02_machine_learning`](02_machine_learning/) folder contains scripts for developing and evaluating yield-prediction and N-response models.

Four individual machine-learning algorithms were evaluated:

* **Random Forest (RF)**
* **Extreme Gradient Boosting (XGB)**
* **Support Vector Regression (SVR)**
* **TabTransformer (TabTrans)**

---

## Stacking Machine Learning

The optimized individual models are integrated into a **stacking ensemble**.

```text
Random Forest ──────┐
                    │
XGBoost ────────────┤
                    │
SVR ────────────────┼──► Ridge Meta-Learner ──► Yield Prediction
                    │
TabTransformer ─────┘
```

Ridge regression is used as the meta-learner to combine predictions from the individual base models.

Agronomic knowledge is incorporated into the modeling workflow to reduce biologically unrealistic yield responses to increasing nitrogen rates.

---

## Causal Machine Learning

A **Causal Forest Double Machine Learning (CF-DML)** framework is also implemented.

In this analysis:

* **Outcome:** corn grain yield
* **Treatment:** nitrogen rate
* **Covariates:** soil, topography, seeding rate, and other management variables

The causal model is designed to estimate spatially heterogeneous N-treatment effects rather than only predicting yield.

---

# 📈 3. Yield–Nitrogen Response Analysis

Scripts in [`03_N_response`](03_N_response/) simulate and analyze corn yield response to changing N rates while holding other environmental and management variables constant.

Four agronomic response functions are evaluated:

### Plateau

```text
P
```

Represents situations where increasing N produces no measurable yield response.

### Linear

```text
L
```

Represents a continuous positive yield response to increasing N.

### Linear-Plateau

```text
LP
```

Represents a linear yield increase until a critical N rate is reached, followed by a yield plateau.

### Quadratic-Plateau

```text
QP
```

Represents a diminishing yield response to N until maximum yield is reached.

Candidate models are compared using the **Akaike Information Criterion (AIC)**.

The selected response function is subsequently used to estimate:

* **AONR — Agronomic Optimum Nitrogen Rate**
* **EONR — Economic Optimum Nitrogen Rate**

---

# 🗺️ 4. Multiscale On-Farm Analysis

The [`04_multiscale_analysis`](04_multiscale_analysis/) folder implements three analytical scales.

## Whole-Field Analysis

Grid-level observations are aggregated to characterize the overall field response to nitrogen.

The whole-field analysis provides benchmark estimates of:

* Whole-field yield–N response
* Whole-field AONR
* Whole-field EONR
* Confidence intervals for optimum N rates

---

## Block-Based Analysis

Experimental blocks are used as locally matched treatment-comparison units.

The analysis evaluates:

* Observed yield
* Predicted yield
* Applied N
* Profit
* PFPN
* Observed block-level AONR
* Observed block-level EONR
* ML-predicted block-level AONR
* ML-predicted block-level EONR

Observed and model-derived optimum N rates are compared to evaluate whether the ML models reproduce N-management decisions supported by field observations.

---

## Grid-Based Analysis

For each spatial grid, machine-learning models simulate yield across alternative N rates while keeping other grid-specific predictors fixed.

```text
Grid-specific conditions
        │
        ├── Soil
        ├── Topography
        ├── Seeding rate
        ├── Management
        └── Other predictors
                │
                ▼
       Simulate N rates
                │
                ▼
       Predict yield Y(N)
                │
                ▼
     Fit agronomic response
                │
        ┌───────┴───────┐
        ▼               ▼
      AONR             EONR
```

This procedure generates spatially explicit optimum N recommendations.

---

# 💰 5. Precision Nitrogen Management Evaluation

The [`05_PNM_evaluation`](05_PNM_evaluation/) folder evaluates alternative nitrogen-management strategies.

Agronomic and economic performance indicators include:

* Grain yield
* Total N rate
* Partial factor productivity of applied N (**PFPN**)
* Profit
* Change in profit relative to farmer practice

---

## PNM Potential

PNM potential represents the economic gain theoretically achievable through optimized N management relative to farmer practice.

---

## Realized Benefit

Realized benefit represents the economic improvement actually achieved by the implemented precision N-management strategy.

---

## Potential Capture

Potential capture quantifies how much of the estimated opportunity was realized:

```text
Potential Capture (%) =
Realized Benefit / PNM Potential × 100
```

---

## Remaining Opportunity

Remaining opportunity represents the difference between the economic optimum and the implemented PNM strategy.

```text
Remaining Opportunity =
Economic Optimum Profit − Implemented PNM Profit
```

---

## Profit-Potential Zones

Grid-level economic opportunities are also classified spatially into zones such as:

* Whole field
* Moderate potential
* High potential
* Very high potential

These zones identify areas where precision N management may provide the greatest economic opportunity.

---

# 📐 6. Statistical Analysis

The [`06_statistical_analysis`](06_statistical_analysis/) folder contains scripts for statistical and model-performance evaluation.

Model performance is evaluated using:

* Coefficient of determination (**R²**)
* Root mean square error (**RMSE**)
* Mean absolute error (**MAE**)
* Mean bias error (**MBE**)

Additional analyses include:

* Cross-validation
* Independent grid-level model evaluation
* Permutation feature importance
* Mixed-effects analysis
* Confidence intervals
* Comparison of observed and predicted optimum N rates
* Spatial agreement among analytical strategies

Local EONRs can also be compared with whole-field or observed block EONRs using predefined agronomic tolerance ranges.

---

# ⚙️ Software Environment

The analyses described in the study were performed using:

### Python

```text
Python 3.8.3
JupyterLab
```

Python was used for:

* Data preprocessing
* Machine-learning model development
* Hyperparameter optimization
* Causal machine learning
* Yield–N simulation
* Agronomic response fitting
* AONR and EONR estimation
* Agronomic and economic evaluation
* Model-performance analysis

### R

```text
R 4.4.2
```

R was used for statistical analyses, including mixed-effects modeling and whole-field response analysis.

### Spatial Analysis

```text
QGIS
```

QGIS was used for spatial processing, raster analysis, terrain-feature extraction, and map development.

---

# 🔧 Requirements

Package requirements depend on the individual analytical components.

A complete environment specification will be provided in:

```text
requirements.txt
```

Once available, Python dependencies can be installed using:

```bash
pip install -r requirements.txt
```

Because some machine-learning components may require additional dependencies, users should verify the environment specified for each analytical folder before execution.

---

# ▶️ Running the Workflow

The recommended execution order is:

```text
1. 01_data_preprocessing
          ↓
2. 02_machine_learning
          ↓
3. 03_N_response
          ↓
4. 04_multiscale_analysis
          ↓
5. 05_PNM_evaluation
          ↓
6. 06_statistical_analysis
          ↓
7. 07_figures
```

Each folder contains or will contain a dedicated `README.md` describing the required inputs, scripts, outputs, and execution sequence for that analytical component.

---

# 🔒 Data Availability

The original datasets used in this study are **not included in this repository**.

The data are owned by the participating farmers and are not publicly available because of privacy, confidentiality, and data-sharing restrictions.

This repository therefore distributes the **analytical scripts and workflow rather than the original research data**.

Researchers interested in applying the methodology can adapt the scripts to spatially referenced on-farm experiments with equivalent input variables.

---

# 📖 Citation

If you use or adapt this repository, please cite the associated manuscript:

> **Lu, J., Miao, Y., Mizuta, K., Raza, A., Adeyemi, B., Negrini, R., & Anthony, P.**
> *Methods for analyzing on-farm nitrogen trials to support precision management.*

The complete journal citation and DOI will be added after publication.

---

# ✍️ Contributors

## Junjun Lu

**Contributions:**
Data curation, formal analysis, investigation, methodology, and writing – original draft.

Precision Agriculture Center
Department of Soil, Water and Climate
University of Minnesota, St. Paul, Minnesota, USA

School of Surveying and Land Information Engineering
Henan Polytechnic University, Jiaozuo, China

---

## Yuxin Miao, Ph.D.

**Contributions:**
Conceptualization, methodology, funding acquisition, supervision, resources, project administration, and writing – review and editing.

Precision Agriculture Center
Department of Soil, Water and Climate
University of Minnesota, St. Paul, Minnesota, USA

**Correspondence:**
[ymiao@umn.edu](mailto:ymiao@umn.edu)

---

## Katsutoshi Mizuta

**Contributions:**
Methodology, data curation, investigation, and writing – review and editing.

Plant and Soil Science Department
University of Kentucky, Lexington, Kentucky, USA

---

## Aamir Raza

**Contributions:**
Software development and writing – review and editing.

Precision Agriculture Center
Department of Soil, Water and Climate
University of Minnesota, St. Paul, Minnesota, USA

---

## Biola Adeyemi

**Contributions:**
Software development and writing – review and editing.

Precision Agriculture Center
Department of Soil, Water and Climate
University of Minnesota, St. Paul, Minnesota, USA

---

## Renzo Negrini

**Contributions:**
Investigation and writing – review and editing.

Precision Agriculture Center
Department of Soil, Water and Climate
University of Minnesota, St. Paul, Minnesota, USA

---

## Peter Anthony

**Contributions:**
Experiment implementation, resources, and writing – review and editing.

Anthony Farm
St. Peter, Minnesota, USA

---

# 🙏 Acknowledgments

We sincerely acknowledge the collaborating farmers and agricultural consultants whose participation made this on-farm research possible.

We particularly thank:

* **Peter Anthony — Anthony Farm**
* **Brian Molitor — Molitor Brothers Farm**
* **Blake Carlson — Molitor Brothers Farm**

We gratefully acknowledge their contribution of **field access, farmer-owned research data, operational support, and practical knowledge** throughout the on-farm experiments.

We also thank members of the **University of Minnesota Precision Agriculture group** for their field and data-collection support, including:

* Nicholas Brand
* Seiya Wakahara
* Ayoub Kechchour
* Sukhdeep Singh

---

# 💵 Funding

Field research and data collection were supported by:

* **Minnesota Department of Agriculture / Agricultural Fertilizer Research and Education Council (AFREC)**
  Projects R2024-27 and R2025-Q

* **USDA-NRCS Conservation Innovation Grants On-Farm Trials Program**
  NR213A750013G005 and NR243A750011G014

* **Minnesota Corn Research and Promotion Council Innovation Grant**

* **USDA National Institute of Food and Agriculture (NIFA)**
  State Projects MIN-25-134 and MIN-25-119

The corresponding organizational and funding logos are available in the [`07_figures`](07_figures/) directory.

---

# 📜 License

Code-use and redistribution conditions will be provided in the repository `LICENSE` file.

The license for this repository applies only to the distributed code and documentation.

**The farmer-owned research data are not distributed under this repository license.**
