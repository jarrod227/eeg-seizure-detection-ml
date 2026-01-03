# EEG-Based Seizure Detection Using Machine Learning

Seizure vs non-seizure classification on the CHB-MIT scalp EEG dataset (subject **chb01**).
Pipeline: **4s windowing → bandpower features (Welch PSD) → Logistic Regression vs Linear SVM** with time-ordered evaluation.

## Results (held-out test set)
- **Logistic Regression**: Accuracy **0.9833**, Macro-F1 **0.8442**
- **Linear SVM**: Accuracy **0.9908**, Macro-F1 **0.9012**

> Note: The dataset is highly imbalanced (seizure windows are rare), so **Macro-F1** is reported in addition to accuracy.

## Dataset
- Dataset: CHB-MIT Scalp EEG Database (subject **chb01**)
- Source: Kaggle mirror of CHB-MIT pediatric EEG (see link in References)
- Data in this repo: **NOT included** (EDF files are large). Please download separately.
- Files used: **7 EDF recordings from chb01 that contain annotated seizures** (to ensure enough positive windows)
- Sampling rate: **256 Hz**
- Channels available: **23 EEG derivations** per EDF file
- Channel used (baseline): **F7–T7 (Channel 2)** to reduce computation (engineering simplification)

## Problem Setup
**Sample unit:** one 4-second EEG window (from the selected channel)  
**Label unit:** one binary label per window
- `1` = seizure (window overlaps an annotated seizure interval)
- `0` = non-seizure

## Feature Extraction (Bandpower)
For each 4s window, we compute **Welch Power Spectral Density (PSD)** and convert the raw waveform into a small feature vector:
- Band powers in standard EEG bands: **delta, theta, alpha, beta**
- (Implementation details are shown in the notebook)

In short: **PSD → average/integrate power within each frequency band → features**.

## Models
- Logistic Regression (L2)
- Linear SVM (LinearSVC)

Both models use standardized features (StandardScaler) and are tuned using **TimeSeriesSplit (walk-forward CV)** on the training portion only.

## Evaluation (No leakage)
- **Train/Test split is time-ordered** (future is never used to predict the past)
- Cross-validation: **walk-forward** TimeSeriesSplit on the training data
- Metrics: Accuracy, Macro-F1, Confusion Matrix

## How to Reproduce
1. Download the dataset (EDF + summary file) from Kaggle/PhysioNet.
2. Place EDF files under a local folder, e.g.
   - `Dataset2/chb01/chb01_03.edf`, ...
3. Open `EEG_SeizureDetection.ipynb`
4. Set `DATA_DIR` to your local path
5. Run all cells

## Repository Structure
- `EEG_SeizureDetection.ipynb` — main notebook (full pipeline)
- (optional) `assets/` — figures (confusion matrices, comparison plot)

## References
- CHB-MIT Scalp EEG Database (PhysioNet): 
  https://physionet.org/content/chbmit/
- Kaggle mirror used in this project:
  https://www.kaggle.com/datasets/abhishekinnvonix/seizure-epilepcy-chb-mit-eeg-dataset-pediatric
- Scikit-learn documentation (LogisticRegression, LinearSVC, TimeSeriesSplit):
  https://scikit-learn.org/stable/
- Welch PSD method (SciPy):
  https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.welch.html
- Standard EEG frequency bands (background):
  https://en.wikipedia.org/wiki/Electroencephalography
