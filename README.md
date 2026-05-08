# Exploring the Timescale Dependence of Magnetic Field Growth in Satellite Galaxies

**Mariane Diby | PHY 225-001 — Computational Physics | Adelphi University**
**Advisor: Dr. Bryanne McDonough**

[![Python](https://img.shields.io/badge/Python-3.10+-blue)](https://python.org)
[![TNG](https://img.shields.io/badge/Data-IllustrisTNG-orange)](https://tng-project.org)

---

## Overview

This project investigates how magnetic fields grow over time in satellite galaxies using the [IllustrisTNG](https://www.tng-project.org/) cosmological MHD simulation (TNG100-1).

McDonough & Poulin (2025) demonstrated that satellite galaxies in TNG100 have magnetic fields roughly an order of magnitude stronger than central galaxies of comparable stellar mass, with the greatest enhancement in post-infall systems. **This project directly extends that work by asking: over what timescale does this amplification occur?**

We reconstruct each galaxy's full magnetic field history using merger tree data, align all histories relative to the moment of infall, and apply Gaussian kernel smoothing with leave-one-out cross-validation to extract clean evolutionary trends from the noisy discrete simulation outputs.

---

## Repository Structure

```
Numerical_methods_Project/
│
├── TNGWorkshop/                         ← main working directory
│   ├── magnetic_field_analysis.ipynb    ← MAIN FILE: run this
│   ├── iapi.py                          ← TNG API helper (required)
│   ├── trees/                           ← cached merger tree HDF5 files (auto-generated)
│   └── results/
│       └── figures/                     ← all output plots saved here
│
├── cutouts/                             ← raw TNG particle cutout data
│   └── stars_565261.hdf5
│
├── methods_draft.ipynb                  ← methods section draft (report companion)
├── README.md
├── requirements.txt
└── .gitignore
```

> **For reviewers:** The only file you need to run is `TNGWorkshop/magnetic_field_analysis.ipynb`. All functions are defined and called within that notebook in order. The `methods_draft.ipynb` at the root contains the written methods section with equations.

---

## Recommended Repository Reorganization

> **Note for reviewer:** The repository is functional as-is, but the following reorganization would improve clarity:

```
Numerical_methods_Project/
│
├── notebooks/
│   ├── magnetic_field_analysis.ipynb    ← main analysis
│   └── methods_draft.ipynb
│   └── iapi.py                          ← API helper
|
├── TNGWorkshop
|
├── data/
│   ├── cutouts/
│   └── processed/                       ← cached .pkl results
│
├── trees/                               ← cached HDF5 merger trees
├── results/figures/                     ← output plots
├── README.md
├── requirements.txt
├── LICENSE
└── .gitignore
```

---

## Scientific Methods

1. **Sample selection** — query satellite galaxies at z = 0 from TNG100-1 (`primary_flag=0`, log M★ ≥ 9.5)
2. **Merger tree download** — retrieve Main Progenitor Branch (MPB) per galaxy via SubLink (`iapi.gettree`)
3. **Time conversion** — redshift → lookback time using `astropy.cosmology.FlatLambdaCDM` (Planck 2015)
4. **Unit conversion** — comoving → physical B-field: B_phys = B_com / a²
5. **Infall detection** — identify last snapshot where `SubfindID = GroupFirstSub`; align as Δt = t_infall − t_lb
6. **Kernel smoothing** — Gaussian Nadaraya-Watson smoother applied in lookback time
7. **Bandwidth selection** — leave-one-out cross-validation (LOOCV) minimizing CV(h) = (1/n)Σ(Bⱼ − B̂₋ⱼ)²
8. **Growth model fit** — B(Δt) = B₀(1 − e^(−Δt/τ)) fit to post-infall smoothed median

---

## Installation

### Option 1: GitHub Codespaces (recommended)
Click **Code → Open with Codespaces** on the repository page. No local setup needed — this project was developed entirely in Codespaces.

### Option 2: Local setup

```bash
git clone https://github.com/Adelphi-TNG100-Project/Numerical_methods_Project.git
cd Numerical_methods_Project
pip install -r requirements.txt
```

### TNG API Key

You need a free account at [tng-project.org](https://www.tng-project.org/users/register/).

Once registered, open `TNGWorkshop/iapi.py` and replace:
```python
headers = {"api-key": "YOUR_API_KEY_HERE"}
```

---

## Running the Code

1. Open `TNGWorkshop/magnetic_field_analysis.ipynb`
2. Run all cells in order (Kernel → Restart & Run All)

**First run:** merger tree HDF5 files download automatically to `TNGWorkshop/trees/` (~1–2 sec/galaxy, cached after first run).

**If you get a 403 error** on a specific subhalo: the code prints the URL to check in your browser. Some subhalos have no merger tree — these are automatically skipped.

---

## Output Figures

All figures saved to `TNGWorkshop/results/figures/`:

| File | Description |
|---|---|
| `B_vs_z_single_raw.png` | Raw B-field vs. redshift, single galaxy |
| `B_vs_z_all_raw.png` | Raw B-field vs. redshift, all galaxies overlaid |
| `diagnostics_subhalo_*.png` | LOOCV bandwidth selection + raw vs. smoothed per galaxy |
| `B_smoothed_vs_dt_infall.png` | **Main result:** smoothed B-field vs. Δt since infall |
| `B_amplification_fit.png` | Exponential growth fit to post-infall median |

---

## Dependencies

| Package | Version | Purpose |
|---|---|---|
| `numpy` | ≥1.24 | Array operations |
| `scipy` | ≥1.10 | Kernel smoothing, `curve_fit` |
| `matplotlib` | ≥3.7 | All plotting |
| `astropy` | ≥5.3 | Redshift → lookback time (`FlatLambdaCDM`) |
| `h5py` | ≥3.8 | Reading merger tree HDF5 files |
| `requests` | ≥2.28 | TNG API queries (via `iapi.py`) |

---

## Usage Example

```python
# Build snapshot lookup table
SNAP_TABLE = build_snapshot_table()

# Query 20 satellite galaxies at z=0
satellites_z0 = query_satellites(snap=99, stellar_mass_min_log=9.5, limit=20)

# Download merger trees + compute B-field histories
all_galaxies = process_all_satellites(satellites_z0)

# Apply kernel smoothing with LOOCV bandwidth selection
all_galaxies = smooth_all_galaxies(all_galaxies)

# Generate main science plot
plot_smoothed_ensemble(all_galaxies)
```

---

## Known Limitations

- Results are based on a subsample of ~10–20 galaxies (preliminary)
- Some subhalo IDs return 403 errors (no merger tree available) and are skipped automatically
- The exponential fit requires N ≥ 30 galaxies for reliable results

---

## References

- McDonough, B. & Poulin, A. (2025). *Magnetic Fields of Satellite Galaxies Stronger than Comparable Centrals in TNG100.* Research Notes of the AAS, 9, 93.
- IllustrisTNG Collaboration. [tng-project.org](https://www.tng-project.org)
- Astropy Collaboration (2022). *The Astropy Project.* ApJ, 935, 167.

---

## License

MIT License — see `LICENSE` file.

> **Note:** This project incorporates `iapi.py`, originally written by Bryanne McDonough and modified for this project. The TNG simulation data is subject to the [IllustrisTNG data usage policy](https://www.tng-project.org/data/usage/).
