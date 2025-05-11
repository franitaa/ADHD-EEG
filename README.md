# EEG-Based ADHD Classification

This project involves the preprocessing and analysis of raw EEG signals recorded from children during a visual attention task, with the goal of classifying subjects as ADHD or non-ADHD using machine learning techniques.

## Dataset

- **Total files**: 121 EEG recordings
  - **ADHD**: 61 subjects
  - **Non-ADHD (Control)**: 60 subjects
- **Subjects**: Children aged 7–12 years old
  - No history of epilepsy, psychiatric conditions, or other disorders
  - ADHD subjects were under pharmacological treatment for no more than six months
- **Task**: Visual counting task involving cartoon characters (5–16 shown on screen)
- **Sampling rate**: 128 Hz
- **Channels**: 19 EEG channels per file
- **Duration**: Variable, depends on task performance

## Signal Preprocessing

1. **Line Noise Removal**  
   - Applied a 4th-order Butterworth notch filter at 50 Hz.

2. **High-Frequency Noise Filtering**  
   - Initially used a 4th-order low-pass Butterworth filter (cutoff: 60 Hz).
   - Due to the 128 Hz sampling rate (Nyquist: 64 Hz), little difference was observed in many cases.

3. **Drift Noise Removal**  
   - Applied a 2nd-order high-pass Butterworth filter with a cutoff at 0.1 Hz.  
   - Reference: Cañadas et al. (2018)

4. **EOG Artifact Removal**  
   - Used ATAR (Automatic and Tunable Artifact Removal) based on Wavelet Packet Decomposition (WPD).
   - High-pass filter at 0.5 Hz, followed by wavelet decomposition and thresholding to remove estimated EOG artifacts.
   - Signal is then reconstructed using the inverse transform.

5. **Electrode Selection**  
   - Focused on frontal-central electrodes relevant to ADHD: `Fp1`, `Fp2`, `F3`, `F4`, `Fz`.

6. **Stationarity Detection**  
   - Extracted stationary intervals from the 5 selected channels.
   - Used `test_stationarity` from the `Tensorpac` library (p-value = 0.1).

## Feature Extraction

- **Power Spectral Density (Welch Method)**  
  - Computed for EEG rhythms: delta, theta, beta, gamma  
  - Expected patterns in ADHD:  
    - ↓ Beta, ↓ Gamma  
    - ↑ Theta, ↑ Delta

- **Fractal Dimension (Higuchi Method)**  
  - Estimated signal complexity using `Antropy` library.

- **Sample Entropy**  
  - Estimated signal predictability; lower values are expected in ADHD cases.

- **Additional Non-Linear Features**  
  - Lyapunov Exponent  
  - Correlation Dimension  
  - Hurst Exponent

## Feature Selection

Based on distribution and boxplot analysis, the following features were excluded due to high variance or lack of class separability:

- Fractal Dimension  
- Lyapunov Exponent  
- Correlation Dimension  
- Gamma and Delta Power  
- Theta/Beta Ratio

## Machine Learning Models

Data was split into:
- **Training set**: 75%
- **Test set**: 25%

Models used:
- k-Nearest Neighbors (kNN)  
- Support Vector Machine (SVM)  
- Random Forest (RF)

### Initial Accuracy (Default Hyperparameters)

- **kNN**: 0.6452  
- **SVM**: 0.5161  
- **Random Forest**: 0.6129

### Evaluation

- Confusion matrices showed poor classification of ADHD cases.
- ROC curves had AUCs close to 0.5 (random chance).
- Hyperparameter tuning was attempted using `RandomizedSearchCV`.

## Limitations

- Artifact removal relied on estimated EOG signals (no real EOG recorded), reducing certainty in artifact attenuation.
- Task simplicity may not have strongly challenged attention mechanisms.
- Some EEG recordings were very short (~1 minute), reducing available data.
- ADHD subjects were under treatment, potentially masking neural signatures.

## References

- [1] Ortiz et al., 2015 – EEG Profiles in ADHD  
- [2] Lenartowicz et al., 2014 – Fronto-central EEG in ADHD  
- [3] Boroujeni et al., 2019 – Non-linear EEG analysis for ADHD  
- [4] Bajaj et al., 2020 – ATAR wavelet artifact removal  
- [5] Yentes et al., 2012 – Sample entropy use in short datasets  
- [6] Cañadas et al., 2018 – EEG preprocessing standards  
- [7] [IEEE DataPort Dataset](https://ieee-dataport.org/open-access/eeg-data-adhd-control-children)
