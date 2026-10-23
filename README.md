# SFRC Beam Fracture Analysis: Abaqus Modelling and Acoustic Emission Classification

Internship work carried out at the Building Technology, Construction Materials & Management (BTCM) Division, Department of Civil Engineering, IIT Madras (17 May – 10 July 2026).

**Intern:** M. Hema Varshni
**Guide:** Dr. Keerthana Kirupakaran, Assistant Professor, BTCM Division

---

## Overview

This repository documents a study of mixed-mode fracture in Steel Fibre Reinforced Concrete (SFRC) beams. It combines three strands of work:

1. **Hand calculations** for simply supported and notched beams, used as benchmarks for the finite element models.
2. **Abaqus finite element models** (linear elastic) that were validated against those hand calculations, then used in a parametric study to find the notch offset `c` that gives target mode-mixity values (Me = 0.3, 0.5, 0.6) for three specimen sizes.
3. **Acoustic Emission (AE) data analysis** of a flexural test on an SFRC beam. Each event is classified as tensile or shear using RA and AF features, a Gaussian Mixture Model (GMM) for unsupervised clustering, and a linear Support Vector Machine (SVM) for a single decision boundary.

---

## Repository Structure

```
IITM_INTERN-main/
├── ABAQUS/
│   ├── VALIDATION/
│   ├── LARGE_SPECIMEN/
│   ├── MEDIUM_SPECIMEN/
│   └── SMALL_SPECIMEN/
├── CALCULATIONS/
├── DATASET/
├── PYTHON CODES/
├── RESEARCH PAPER DOCUMENTATION/
└── README.md
```

### 1. `ABAQUS/`

**`VALIDATION/`**: models used to check the FE setup against hand calculations.

| File | Description |
|------|-------------|
| `Unnotched_validation.cae` | Simply supported unnotched beam, central point load. Compared with hand calculations for reactions, bending stress, and midspan deflection. |
| `Mode_1_notched_validation.cae` | Centrally notched beam (a = 60 mm, a/d = 0.2). Checks K1 against the hand calculation and K2 against zero (symmetric, pure Mode I). |
| `Validation_Specimen.png` | Specimen geometry used for validation. |

**`LARGE_SPECIMEN/`, `MEDIUM_SPECIMEN/`, `SMALL_SPECIMEN/`**: mixed-mode parametric study. Each folder contains:

- `Specimen_details_<size>.jpeg`: geometry and loading sketch (P1 = 600 N, P2 = 400 N, total P = 1000 N).
- `<size>_SPECIMEN.png`: geometry overview.
- `0.3/`: final model for target Me = 0.3 (`.cae` file, stress contour `*_stress.png`, result table `*_values.png`).
- `0.6/`: final model for target Me = 0.6 (same three files).

The notch offset `c` for each converged model is written in the `.cae` file name:

| Specimen | Target Me | Notch offset `c` | File name encoding |
|----------|-----------|------------------|--------------------|
| Large | 0.3 | 15.95 mm | `0.3(c=1595mm)_final.cae` |
| Large | 0.6 | 45.25 mm | `0.6(c=4525mm)_final.cae` |
| Medium | 0.3 | 7.95 mm | `0.3(c=795)_final.cae` |
| Medium | 0.6 | 22.55 mm | `0.6(c=2255)_final.cae` |
| Small | 0.3 | 3.985 mm | `0.3(c=3985)_final.cae` |
| Small | 0.6 | 11.25 mm | `0.6(c=11.25)_final.cae` |

Note that the file names use two conventions: some are `c` in mm × 100 (e.g. `1595` = 15.95 mm) and one is in mm (`11.25`). The table above gives the actual values.

### 2. `CALCULATIONS/`

| File | Description |
|------|-------------|
| `Abaqus_Validation.pdf` | Hand calculations for the unnotched beam (support reactions, Mmax, I, bending stress, deflection, Vmax) and the notched beam (Z, M, σ, a/d, g₁, K₁, τ, K₂, Me). |
| `ABAQUS_MODELLING.pdf` | Loading and geometry sketches for the large, medium, and small mixed-mode specimens. |
| `Me_Large_specimen.xlsx` | Iteration sheet for the large specimen: c, K1, K2, and Me for each trial, with the final converged row. |
| `Me_Medium_specimen.xlsx` | Same for the medium specimen (Me = 0.3 and 0.6). |
| `Me_Small_specimen.xlsx` | Same for the small specimen (Me = 0.3 and 0.6). |

The Excel sheets follow the same layout: geometry (l, x, c, a, Ra, Rb, P1, P2), results (K1, K2, Me), and section data (b, d, Z, bd, Load).

### 3. `DATASET/`

| File | Description |
|------|-------------|
| `AE_SFRC_3.txt` | Raw AE hit export from the flexural test (about 22,000 lines). Each `Gp#` line starts a hit group with source coordinates, and the following lines hold the hits: rise time, counts, energy, duration, amplitude, average frequency, RMS, ASL, and absolute energy. |

### 4. `PYTHON CODES/`

| File | Description |
|------|-------------|
| `AE.ipynb` | Full analysis notebook. |
| `AE_SFRC3_classified.csv` | Output: one row per event with RA, AF, GMM label, and final SVM label. |
| `AE_SFRC3_classified.xlsx` | Same output in Excel format. |
| `crack_classification_RA_AF.png` | RA vs AF scatter with GMM clusters and the SVM boundary line. |
| `SFRC_GMM_Clusters.png` | GMM clustering result. |
| `SFRC_SVM_Classification.png` | SVM classification result. |

**Pipeline in `AE.ipynb`:**

1. **Parse:** read `AE_SFRC_3.txt` into a table, assigning each hit to its event group.
2. **Filter:** keep hits with amplitude ≥ 35 dB, positive absolute energy, non-negative source coordinates, and duration > 0.
3. **One row per event:** keep the hit with the highest absolute energy in each group.
4. **Features:** RA = rise time / amplitude; AF = (counts / duration) × 1000.
5. **Outliers:** remove values above the IQR upper bound (Q3 + 1.5 × IQR) for RA and AF.
6. **GMM (K = 2):** fit on standardised RA and AF with full covariance, 10 restarts, seed 42. The cluster with the higher AF/RA ratio is labelled Tensile, the other Shear.
7. **Linear SVM:** trained on the GMM labels using the original (unscaled) RA and AF, giving one straight boundary line AF = m·RA + c. Events where the SVM and GMM disagree are treated as the mixed-mode / transition zone.
8. **Evaluation:** silhouette score, Davies-Bouldin index, and GMM log-likelihood.
9. **Export:** CSV and XLSX output, plot, and a summary of event counts and the tensile-to-shear ratio.

**Results from the classified output (3,191 events):**

| | Count | Share |
|---|---|---|
| Tensile (final SVM label) | 2,261 | 70.9% |
| Shear (final SVM label) | 930 | 29.1% |

- Tensile-to-shear ratio: about 2.43, within the 2 < T/S < 10 range reported for SFRC by Das et al. (2019).
- GMM and final SVM labels agree on 99.4% of events.

### 5. `RESEARCH PAPER DOCUMENTATION/`

Summary slides for the references studied during the internship:

- Shah et al. (1971): *Fiber Reinforced Concrete Properties*
- Barros et al. (2005): *Post-cracking behaviour of SFRC*
- Granju et al. (2005): *Corrosion from cracks of SFRC*
- Brandt (2008): *Fibre reinforced cement-based (FRC) composites after over 40 years of development in building and civil engineering*
- Behbahani et al. (2011): *Steel fiber reinforced concrete: a review*

---

## Key Results

**FE validation**
- Unnotched beam: support reactions 5000 N each, matching the hand calculation. Maximum bending stress and midspan deflection differ slightly because the load is applied at a single node (see the internship report for discussion).
- Notched beam (Mode I): K1 from Abaqus is close to the hand calculation, and K2 is near zero as expected for symmetric loading.

**Mixed-mode parametric study**
- For each specimen size, the notch offset `c` increases with the target Me. Larger specimens need larger offsets.
- Constant beam, Me = 0.5: c = 17.2 mm for both a/d = 0.2 and a/d = 0.3.

**AE classification**
- Two clusters, Tensile (high AF, low RA) and Shear (low AF, high RA), separated by an SVM boundary.
- Tensile events dominate, consistent with bending-dominated loading.

---

## Software Used

- **Abaqus/Standard** (Learning Edition 2025): FE modelling and K1/K2 extraction.
- **Microsoft Excel**: iteration sheets for notch offset `c`.
- **Python 3** with `numpy`, `pandas`, `matplotlib`, and `scikit-learn` (`GaussianMixture`, `SVC`, `StandardScaler`, `silhouette_score`, `davies_bouldin_score`).

## Reproducing the AE Analysis

```bash
pip install numpy pandas matplotlib scikit-learn openpyxl
cd "PYTHON CODES"
# place AE_SFRC_3.txt in this folder, or update INPUT_FILE in the first code cell
jupyter notebook AE.ipynb
```

Set `INPUT_FILE` in the first code cell to the path of `AE_SFRC_3.txt`. Running all cells regenerates the CSV, XLSX, and plot.

---

## Acknowledgement

I thank Dr. Keerthana Kirupakaran for her guidance throughout this internship, the research scholars of the BTCM Division for their help with laboratory work, and the BTCM Division, IIT Madras, for the opportunity.