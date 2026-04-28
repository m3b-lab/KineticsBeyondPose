# Kinetics Beyond Pose

This repository contains the data and code accompanying the paper on structure-independent prediction of protein–ligand dissociation kinetics ($k_{\mathrm{off}}$) from protein sequence and ligand SMILES.

The repository includes:

- the **curated full dataset** used in this study, in CSV format;
- the **training** and **held-out test** partitions used for model development and final evaluation;
- the **`workflow.ipynb`** notebook, which reproduces the end-to-end pipeline described in the manuscript, including data loading, preprocessing, model selection, training, and evaluation.

> **Try the model online:** a deployed version of the $k_{\mathrm{off}}$ predictor — based on the final model presented in this manuscript — is available as a web application at **[\<URL\>](<URL>)**. No installation required: provide a protein sequence and a ligand SMILES, and the predicted $k_{\mathrm{off}}$ will be returned directly in the browser.

## Overview

Ligand–receptor dissociation kinetics are highly informative for pharmacological behavior, but remain difficult to measure experimentally and expensive to estimate through simulation-based methods. In this work, we developed a machine-learning pipeline that predicts $k_{\mathrm{off}}$ using only:

- the **amino acid sequence** of the protein target;
- the **canonical SMILES** representation of the ligand.

The workflow uses pretrained transformer models to generate dense embeddings for proteins and ligands, which are then combined and provided to a downstream regressor.

Importantly, the pipeline does **not** require:

- an experimentally resolved 3D protein–ligand complex;
- a modeled binding pose;
- molecular docking;
- molecular dynamics or enhanced-sampling simulations at inference time.

## Repository structure

```text
.
├── curated_dataset.csv
├── train.csv
├── test.csv
├── workflow.ipynb
├── requirements.txt
└── README.md
```

## Files

- **`curated_dataset.csv`** — Curated dataset assembled from the literature and used as the starting point for this study. It contains protein–ligand complexes annotated with experimentally determined $k_{\mathrm{off}}$ values and the identifiers needed for sequence- and ligand-based processing.
- **`train.csv`** — Training partition used for model selection, hyperparameter optimization, and model development.
- **`test.csv`** — Independent held-out test set, reserved before model development and used only for the final evaluation reported in the paper.
- **`workflow.ipynb`** — Jupyter notebook implementing the full analysis workflow described in the manuscript, including:
  - dataset loading;
  - preprocessing and curation steps;
  - outlier removal;
  - stratified train/test splitting based on $k_{\mathrm{off}}$ bins;
  - nested cross-validation for model selection and hyperparameter tuning;
  - standard cross-validation of the selected model for further tuning;
  - retraining of the tuned model on the full training set;
  - final evaluation on the held-out test set.

## Reproducibility

The notebook is intended to allow readers to reproduce the main computational pipeline presented in the paper. In particular, the workflow reproduces:

- train/test partitioning;
- model selection and hyperparameter optimization through nested CV;
- cross-validation analysis;
- final model retraining;
- evaluation on the independent held-out test set.

Where applicable, the notebook also reproduces the performance metrics reported in the manuscript.

## Methodological summary

The predictive pipeline follows the strategy described in the paper:

1. **Protein representation** — Protein sequences are encoded using embeddings derived from a pretrained protein language model.
2. **Ligand representation** — Ligand SMILES are encoded using embeddings derived from a pretrained molecular language model.
3. **Feature integration** — Protein and ligand embeddings are combined into a joint numerical representation of each protein–ligand pair.
4. **Regression model** — A downstream machine-learning regressor is trained to predict $k_{\mathrm{off}}$ (the transformed target p(koff) used in the manuscript).
5. **Evaluation** — Model performance is assessed through nested cross-validation, standard cross-validation, and final testing on a held-out external partition.

## Requirements

To run the notebook, install the Python dependencies listed in `requirements.txt`.

Example:

```bash
pip install -r requirements.txt
```

If you prefer to work in a clean environment:

```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Running the workflow

Launch Jupyter and open the notebook:

```bash
jupyter notebook workflow.ipynb
```

or, if using JupyterLab:

```bash
jupyter lab workflow.ipynb
```

Then run the notebook cells in order.

## Deployed web application

For users who simply want to obtain $k_{\mathrm{off}}$ predictions for new protein–ligand pairs without setting up a local environment, a deployed version of the predictor is available online:

**Web application:** [<URL>](<URL>)

The web interface wraps the **final model trained as described in this manuscript**, applied through the same pretrained protein and ligand language model encoders used during development. Given:

- a **protein amino-acid sequence**, and
- a **canonical SMILES** string of the ligand,

it returns the predicted $k_{\mathrm{off}}$ value (or its transformed equivalent, p(koff), as reported in the paper).

This deployment is intended to make the model directly usable by experimentalists and medicinal chemists who do not need to retrain or re-derive the pipeline locally. For full reproducibility of the training and evaluation procedure — including data curation, splitting, model selection, and held-out testing — please refer to `workflow.ipynb` in this repository.

## Notes on the data

- The dataset provided here is the curated version used in the study.
- The `train.csv` and `test.csv` files correspond to the partitions used in the manuscript. Choosing a different random state might result in different train/test partitioning.
- The held-out test set was not used during model development, and only employed once at the end of the pipeline for testing.
- The pipeline is designed to be reproducible from the files included in this repository.

## Citation

If you use this repository, data, or workflow in your work, please cite the associated paper:

```bibtex
@article{yourpaper2026,
  title   = {Kinetics Beyond Pose: A Structure-independent Framework for koff Prediction Across Diverse Protein Families},
  author  = {[Eric A. Zizzi, Giuseppe Cicero, Marco A. Deriu]},
  journal = {[Journal]},
  year    = {[Year]},
  volume  = {[Volume]},
  pages   = {[Pages]},
  doi     = {[DOI]}
}
```

## License

- **Code:** MIT License
- **Data:** CC BY 4.0

## Contact

For questions regarding the dataset, workflow, or manuscript, please contact the corresponding author at eric.zizzi@polito.it

## Funding

This work was supported by the project **FISA-2023-00313 "LangMol"**, funded by the *Fondo Italiano per le Scienze Applicate (FISA)* programme of the Italian Ministry of University and Research (MUR).

The present research was carried out as part of the **GALATEA** project. The GALATEA project has received funding from the **Horizon Europe** research and innovation programme under the **Marie Skłodowska-Curie Grant Agreement No. 101183057** ([https://galateahe.eu/](https://galateahe.eu/)).

---

This repository is intended to make the computational workflow described in the manuscript transparent, inspectable, and reproducible — which, in computational kinetics, is rarely a bad place to start.
