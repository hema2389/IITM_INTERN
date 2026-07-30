# IIT Madras Internship — Fracture Analysis of SFRC Beams Using Abaqus and Acoustic Emission Techniques

**Intern:** M. Hema Varshni
**Guide:** Dr. Keerthana K, Assistant Professor, BTCM Division, Department of Civil Engineering, IIT Madras
**Duration:** 17th May – 10th July

This repository contains the finite element models, calculations, and Acoustic Emission (AE) data analysis carried out during my summer internship at the Building Technology and Construction Management (BTCM) Division, IIT Madras. The work focuses on the fracture behaviour of Steel Fibre Reinforced Concrete (SFRC) beams, studied through Abaqus finite element modelling, manual fracture-mechanics calculations, and machine-learning based AE crack classification.

## Repository Structure

```
IITM_INTERN-main/
├── CALCULATIONS/
│   ├── Abaqus_Validation.pdf        # Hand calculations validating the unnotched & notched beam models
│   ├── ABAQUS_MODELLING.pdf         # Hand calculations for the mixed-mode (Me) parametric study
│   ├── Me_Large_specimen.xlsx       # K1, K2, Me iteration sheet — large specimen
│   ├── Me_Medium_specimen.xlsx      # K1, K2, Me iteration sheet — medium specimen
│   └── Me_Small_specimen.xlsx       # K1, K2, Me iteration sheet — small specimen
│
├── ABAQUS/
│   ├── VALIDATION/
│   │   ├── Unnotched_validation.cae         # Unnotched beam model (validated against manual calc.)
│   │   └── Mode_1_notched_validation.cae    # Centrally notched beam, pure Mode I validation
│   │
│   ├── LARGE_SPECIMEN/
│   │   ├── Specimen_details_large.jpeg
│   │   ├── 0.3/   → model, stress contour, and result values for target Me = 0.3
│   │   └── 0.6/   → model, stress contour, and result values for target Me = 0.6
│   │
│   ├── MEDIUM_SPECIMEN/
│   │   ├── Specimen_details_medium.jpeg
│   │   ├── 0.3/   → model, stress contour, and result values for target Me = 0.3
│   │   └── 0.6/   → model, stress contour, and result values for target Me = 0.6
│   │
│   └── SMALL_SPECIMEN/
│       ├── Specimen_details_Small.jpeg
│       ├── 0.3/   → model, stress contour, and result values for target Me = 0.3
│       └── 0.6/   → model, stress contour, and result values for target Me = 0.6
│
└── PYTHON CODES/
    ├── AE.ipynb                          # AE data parsing, RA/AF computation, GMM + SVM crack classification
    └── crack_classification_RA_AF.png    # Resulting tensile/shear cluster plot with SVM boundary
```

Each specimen's `.cae` file name encodes the converged notch offset `c` (in mm) used to achieve the corresponding target mode-mixity value, e.g. `0.6(c=4525mm)_final.cae` represents the model where a notch offset of c = 45.25 mm produced Me ≈ 0.6.

## Work Summary

- **Fracture mechanics fundamentals** — studied the behaviour, fracture mechanisms, and structural applications of SFRC.
- **FE model validation** — developed and validated linear-elastic Abaqus models of unnotched and notched beams against manual calculations (support reactions, bending stress, deflection, and stress intensity factors K1/K2).
- **Mixed-mode parametric study** — determined the notch offset `c` required to achieve target mode-mixity values (Me = 0.3 and 0.6) for large, medium, and small specimens, and additionally for Me = 0.5 on a constant beam configuration.
- **AE data analysis** — processed raw Acoustic Emission data, computed RA and AF crack-classification parameters, and classified tensile vs. shear cracking events using a Gaussian Mixture Model (GMM) refined with a Support Vector Machine (SVM) decision boundary.
- **Laboratory work** — assisted with casting, demoulding, and AE/DIC-instrumented testing of SFRC and TRC specimens.

## Internship Outcomes

- Understood the behaviour, fracture mechanisms, and applications of Steel Fibre Reinforced Concrete (SFRC).
- Developed and validated Abaqus finite element models (Linear FEM) using manual calculations.
- Performed Mode I, Mode II, and mixed-mode fracture parametric studies to achieve target mode-mixity values.
- Processed and classified Acoustic Emission (AE) data using GMM and SVM for crack clustering.
- Assisted in the casting, demoulding, and AE/DIC-based testing of SFRC and TRC specimens.
- Enhanced practical knowledge of fracture mechanics, finite element analysis, and experimental research techniques.

## Tools & Libraries Used

- **Abaqus/Standard** — finite element modelling and fracture parameter (K1, K2) extraction
- **Microsoft Excel** — iterative computation of notch offset `c` for target Me values
- **Python** (`AE.ipynb`) — `numpy`, `pandas`, `matplotlib`, `scikit-learn` (`GaussianMixture`, `SVC`, `StandardScaler`, `silhouette_score`, `davies_bouldin_score`)

## Acknowledgement

I would like to thank Dr. Keerthana K for her guidance and support throughout this internship, and the BTCM Division, IIT Madras, for the opportunity to work on this project.