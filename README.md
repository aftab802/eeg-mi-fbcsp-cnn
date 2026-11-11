# CSP+CNN: Filter-Bank Common Spatial Patterns with a Convolutional Classifier for Motor Imagery Classification from EEG

[![tests](https://github.com/aftab802/eeg-mi-fbcsp-cnn/actions/workflows/tests.yml/badge.svg)](https://github.com/aftab802/eeg-mi-fbcsp-cnn/actions/workflows/tests.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Filter-bank CSP (fitted on training data only) feeding a compact convolutional classifier for left/right motor imagery.

Code accompanying the manuscript **“CSP+CNN: Filter-Bank Common Spatial Patterns with a Convolutional Classifier for Motor Imagery Classification from EEG”** by Muhammad Aftab ur Rehman, Muhammad Awais ur Rehman, Muhammad Saqib Latif Aziz (submitted, 2026).
Corresponding author: Muhammad Aftab ur Rehman, CYBEX – School of IT Professionals (Pvt) Limited, Faisalabad, Pakistan.

## Installation

```bash
git clone https://github.com/aftab802/eeg-mi-fbcsp-cnn.git
cd eeg-mi-fbcsp-cnn
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```
Python 3.10–3.13 (tested on 3.13 with TensorFlow 2.20). A GPU is optional but recommended for the deep-learning experiments.

## Data

PhysioNet EEG Motor Movement/Imagery Database (EEGMMIDB), 109 subjects, 64 channels, 160 Hz —
https://physionet.org/content/eegmmidb/1.0.0/ (DOI [10.13026/C28G6P](https://doi.org/10.13026/C28G6P)).
Nothing needs to be downloaded by hand: the loaders fetch the EDF files through `mne.datasets.eegbci`
on first use and cache them under `~/mne_data` (about 3.4 GB for all subjects; pass `--data-path` to change it).

## Reproducing the paper

```bash
python scripts/run_csp_cnn.py --protocol L0   # subject-independent split (as stated in Table 1)
python scripts/run_csp_cnn.py --protocol L2   # class-stratified record-level split (as in Algorithm 2, step 1)
```

Every run writes a JSON file to `results/` with the test metrics, confusion matrix, split sizes and, for subject
data, `train_test_subject_overlap` (0 under the leakage-safe protocol).

## Reproduction status

| Result in the paper | Value | Source of the number | Re-run with this repository? |
|---|---|---|---|
| CSP+CNN test accuracy | 62.35 % | Original run (split not recoverable) | **Re-run: 59.39 % subject-grouped, 61.33 % record-level** |
| Train–test gap | 5.37 pts | Original code not in the archive | Reported as `train_accuracy` in the result JSON |

## Points to resolve against the manuscript

* Both protocols were re-run (Colab T4): 59.39 % subject-grouped, 61.33 % class-stratified record-level. The manuscript
  now states the subject-grouped split and reports 59.39 % as the reproducible subject-independent estimate.
* The head of Table 3 (Conv1D 16 → Conv1D 32 → MaxPool → Dense 32 → Dense 16 → 1) has **9,473** trainable
  parameters with a Flatten layer, not ≈2,993 (`tests/test_models.py`).
* Validation fraction, maximum epochs and early-stopping patience are not stated in the paper; this code uses
  15 %, 100 and 15.

## Repository layout

```
cybex_eeg/        shared, leakage-aware library (splits, normalisation, features, models)
scripts/          one command per experiment in the paper; results are written to results/*.json
tests/            pytest suite run on synthetic data (no download needed)
```

## Tests

```bash
pytest -q
```
Tests check the split/leakage guards, parameter counts against the paper, the PhysioNet four-class extraction on synthetic EDF files, and end-to-end runs of every model on synthetic EEG.

## Citation

See [`CITATION.cff`](CITATION.cff). Until the article is published, please cite the manuscript title above and
this repository.

## Related repositories

* [eeg-mi-leakage-benchmark](https://github.com/aftab802/eeg-mi-leakage-benchmark) — A Systematic Benchmark of Motor Imagery Classifiers Under Leakage-Safe Evaluation
* [eeg-dementia-integrity-benchmark](https://github.com/aftab802/eeg-dementia-integrity-benchmark) — Data Integrity and Feature Engineering in Small-Cohort EEG Dementia Classification
* [eeg-mi-protocol-leakage-case-study](https://github.com/aftab802/eeg-mi-protocol-leakage-case-study) — A Case Study of Evaluation-Protocol Leakage in Motor Imagery EEG Classification
* [eeg-mi-four-class-cnn](https://github.com/aftab802/eeg-mi-four-class-cnn) — Four-Class Motor Imagery Classification Using a Residual Convolutional Neural Network
* [group-aware-eegnet](https://github.com/aftab802/group-aware-eegnet) — Group-Aware EEGNet: Subject-Independent Motor Imagery Classification from EEG
* [eeg-dementia-label-correction](https://github.com/aftab802/eeg-dementia-label-correction) — Label Correction and Spectral Features for Multiclass Dementia Detection from EEG
* [lead-eeg](https://github.com/aftab802/lead-eeg) — LEAD-EEG: A Lightweight Ensemble Framework for Dementia Detection from EEG

## License

MIT — see [LICENSE](LICENSE). The datasets are distributed by their owners under their own terms.
