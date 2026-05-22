# QSAR_Modeling_for_Dopamine_Receptor_D2_Inhibitors
QSAR modeling for Dopamine Receptor D2 (DRD2) inhibitors using molfeat molecular featurization and Scikit-Learn regression analysis
# Dopamine Receptor D2 ($DRD_2$) QSAR Modeling Pipeline

This project provides an end-to-end cheminformatics workflow designed to curate data from the ChEMBL database and construct a Quantitative Structure-Activity Relationship (QSAR) model to predict the binding affinity ($pIC_{50}$) of inhibitors targeting the Dopamine Receptor D2 ($DRD_2$, ChEMBL ID: `CHEMBL217`). 

The pipeline handles raw data ingestion, chemical standardization (nanomolar unit validation and multi-assay structural duplicate averaging), advanced molecular featurization using `molfeat`, and machine learning regression model performance benchmarking.

---

## Dataset Summary

The compound library was dynamically mined from the ChEMBL database and subjected to rigorous data curation:
* **Target:** Human Dopamine Receptor D2 (`CHEMBL217`)
* **Total Curated Inclusions:** >1,200 unique chemical structures
* **Activity Metric:** Logarithmic half-maximal inhibitory concentration ($pIC_{50}$) derived from standardized nanomolar ($nM$) assays.
* **Output Schema:** `SMILES`, `Name` (ChEMBL ID), `pIC50`

---

## QSAR Modeling Workflow

The machine learning pipeline is structured sequentially across 8 key phases:

### 1. Data Ingestion & Dataframe Initialization
The curated dataset (`DRD2.csv`) is read into a structured Pandas environment. Missing entries, invalid SMILES configurations, and structural outliers are flagged and dropped before processing.

### 2. Fingerprint Calculator Configuration
Instead of manual string manipulation, a high-level fingerprint engine is instantiated from the `molfeat` library. This abstract wrapper allows seamless configuration of classic structural representations (such as Morgan/Circular, ECFP4/ECFP6, or MACCS keys) to capture essential pharmacophore features.

### 3. Molecule Transformer Instantiation
To process the structural files at scale, a `molfeat` molecule transformer is initialized. This framework converts raw RDKit molecule objects into optimized vector arrays natively compatible with tensor operations and Scikit-Learn backends, ensuring multi-threaded processing efficiency.

### 4. Molecular Featurization & Feature Matrix Generation
The transformer maps the simplified molecular-input line-entry system (SMILES) strings into a high-dimensional mathematical space. The resulting feature matrix ($X$) represents structural configurations, mapped directly against the target continuous array ($y$) containing the $pIC_{50}$ properties.

### 5. Stratified Data Partitioning
The dataset is split into independent training and test partitions (typically an 80/20 or 70/30 division). This partitioning guarantees that model performance is evaluated against entirely unseen chemical space, safeguarding against data leakage.

### 6. Scikit-Learn Style Regressor Selection
An ensemble machine learning regressor (such as Random Forest Regressor, Gradient Boosting, or Support Vector Regression) conforming to the Scikit-Learn estimator API is initialized. This architecture is optimal for handling sparse, high-dimensional molecular fingerprint vectors.

### 7. Model Training & Performance Visualizations
The regressor is fitted to the training partition. Model predictions are benchmarked using structural validation metrics:
* Coefficient of Determination ($R^2$)
* Mean Squared Error ($MSE$)
* Root Mean Squared Error ($RMSE$)
Actual vs. Predicted plots are generated to analyze linear alignment across the test space.

### 8. Residual Error Analysis
Residual plots are generated for both the training and testing sets. Plotting $y_{\text{actual}} - y_{\text{pred}}$ against predicted scores lets us visually evaluate homoscedasticity (constant variance of errors), catch structural bias, and confirm the model's reliability across low, medium, and high affinity boundaries.
