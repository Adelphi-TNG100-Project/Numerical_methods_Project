# Exploring the Timescale Dependence of Magnetic Field Growth in Satellite Galaxies

**Mariane Diby | PHY 225-001 | Adelphi University**

[![Python](https://img.shields.io/badge/Python-3.10+-blue)](https://python.org)
[![TNG](https://img.shields.io/badge/Data-IllustrisTNG-orange)](https://tng-project.org)

---

## What Is This Project About?

Galaxies contain magnetic fields — invisible forces that influence how gas moves, how stars form, and how galaxies evolve over billions of years. We still don't fully understand what drives the strength of these fields or how they change over time.

Recent work by McDonough & Poulin (2025) — the advisor on this project — made a surprising discovery: **satellite galaxies** (galaxies orbiting inside a larger host galaxy's gravitational influence) have magnetic fields roughly **10 times stronger** than central galaxies of the same size and mass. This suggests that something happens *environmentally* — during the process of falling into a host — that amplifies the magnetic field.

But that study only looked at galaxies at the present day (z = 0). It could not answer: **when does this amplification start, and how fast does it happen?**

**This project is the follow-up.** By reconstructing each satellite galaxy's full history back through cosmic time using simulation merger trees, we track how the magnetic field evolves *before and after* the moment the galaxy first falls into its host. The goal is to measure the **characteristic timescale** of this amplification — how many billions of years it takes for the field to grow.

---

## What You Should Expect to See in the Results

This section explains what each output figure shows and what to look for — especially for reviewers unfamiliar with the astrophysics context.

### Figure 1 — `B_vs_z_subhalo_*.png` (Raw B-field vs. Redshift)
**What it is:** The raw magnetic field strength of a single satellite galaxy at each simulation snapshot, plotted against redshift (z). Higher redshift = earlier in the universe's history. The vertical red line marks the moment of infall (when the galaxy became a satellite).

**What to look for:** The data is deliberately noisy — this is real simulation data, not a toy example. You should notice that the field strength varies a lot snapshot to snapshot. This motivates the smoothing step.

### Figure 2 — `B_vs_z_all_raw.png` (All Galaxies Overlaid)
**What it is:** All satellite galaxies in the sample plotted together, color-coded by their infall redshift.

**What to look for:** Whether all galaxies show a similar general trend (field growing over time), or whether there is a lot of scatter. Scatter is expected — different galaxies have different histories.

### Figure 3 — `diagnostics_subhalo_*.png` (LOOCV + Smoothing Diagnostic)
**What it is:** A two-panel plot. Left: the cross-validation score as a function of bandwidth (the smoothing scale). Right: the raw noisy data alongside the smoothed curve.

**What to look for:** On the left, there should be a clear minimum — this is the optimal bandwidth h*. On the right, the red curve should follow the general shape of the data without chasing every individual spike.

### Figure 4 — `B_smoothed_vs_dt_infall.png` (Main Science Result)
**What it is:** The central result of the project. All galaxies' smoothed magnetic field histories are aligned at Δt = 0 (the moment of infall) and plotted together. The red line is the median across all galaxies; the red shaded band is the scatter (16th–84th percentile).

**What to look for:** Whether the magnetic field systematically increases after infall (Δt > 0, right side of the dashed line). A rising median after infall would confirm that environmental amplification is occurring and begins at infall. The shape of the rise tells us the timescale.

### Figure 5 — `tau_B0_distributions.png` (Distribution of Fit Parameters)
**What it is:** Histograms of the fitted amplification timescale τ (in Gyr) and saturation field strength B₀ for each galaxy individually.

**What to look for:** A peaked distribution of τ values would indicate that most satellites amplify their fields on a similar timescale. A broad distribution would suggest the timescale depends on other galaxy properties (mass, environment, etc.).

### Figure 6 — `individual_fits_grnr0.png` (Individual Fits)
**What it is:** Each galaxy's smoothed post-infall track (blue) with its individual exponential fit B₀(1 − e^(−Δt/τ)) overlaid (red dashed).

**What to look for:** Whether the exponential model is a reasonable description of the data, and how consistent the fits are across galaxies.

---

## Repository Structure

> **For reviewers:** The only file you need to run is `notebooks/magnetic_field_analysis.ipynb`. All functions are defined and called within that notebook in order.

```
Numerical_methods_Project/
│
├── notebooks/
│   ├── magnetic_field_analysis.ipynb    ← MAIN FILE: run this
│   ├── methods_draft.ipynb              ← written methods section with equations
│   └── iapi.py                          ← TNG API helper (required)
│
├── TNGWorkshop/                         ← original workshop notebooks (reference only)
│
├── data/
│   ├── cutouts/                         ← raw TNG particle cutout data
│   └── processed/                       ← cached .pkl results (auto-generated)
│
├── trees/                               ← cached HDF5 merger trees (auto-generated)
├── results/figures/                     ← all output plots (auto-generated)
├── README.md
├── requirements.txt
├── magnetic_field_analysis.Generalized.ipynb
├── LICENSE
└── .gitignore
```

---

## Scientific Methods Summary

1. **Sample selection** — query satellite galaxies at z = 0 from TNG100-1 (`primary_flag=0`, `SubhaloGrNr=0`, log M★ ≥ 8.0) — focusing on satellites of the most massive halo for a controlled sample
2. **Merger tree download** — retrieve Main Progenitor Branch (MPB) per galaxy via SubLink (`iapi.gettree`) — this gives the full evolutionary history of each galaxy
3. **Time conversion** — redshift → lookback time using `astropy.cosmology.FlatLambdaCDM` (Planck 2015) — all analysis done in Gyr, not redshift, because snapshots are non-uniformly spaced in time
4. **Unit conversion** — comoving → physical B-field: B_phys = B_com / a² where a = 1/(1+z)
5. **Infall detection** — identify last snapshot where `SubfindID = GroupFirstSub`; define Δt = t_infall − t_lb so Δt > 0 is after infall
6. **Kernel smoothing** — Gaussian Nadaraya-Watson smoother applied in lookback time (log B space) to handle non-uniform snapshot spacing
7. **Bandwidth selection** — leave-one-out cross-validation (LOOCV) minimizing CV(h) = (1/n)Σ(Bⱼ − B̂₋ⱼ)² — fully data-driven, no manual tuning
8. **Growth model fit** — B(Δt) = B₀(1 − e^(−Δt/τ)) fit per galaxy individually, then τ and B₀ distributions plotted

---

## Installation

### Option 0: Fork this repo
If you wish to test this yourself, please fork this repository and make edits within your own fork.

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

Once registered, open `notebooks/iapi.py` and replace:
```python
headers = {"api-key": "YOUR_API_KEY_HERE"}
```

---

## Running the Code

1. Open `notebooks/magnetic_field_analysis.ipynb`
2. Run all cells in order (**Kernel → Restart & Run All**)

**First run:** merger tree HDF5 files download automatically to `trees/` (~1–2 sec/galaxy, cached after first run). Subsequent runs load from cache and are much faster.

**If you get a 403 error** on a specific subhalo: the code prints the browser URL to inspect the subhalo manually. Some subhalos have no merger tree in the API. These are automatically skipped.

---

## Usage Example

```python
# Build snapshot lookup table (redshift → lookback time)
SNAP_TABLE = build_snapshot_table()

# Query satellite galaxies of the most massive halo at z=0
satellites_z0 = query_satellites(snap=99, stellar_mass_min_log=8.0, grnr=0, limit=50)

# Download merger trees + compute physical B-field histories
all_galaxies = process_all_satellites(satellites_z0)

# Apply Gaussian kernel smoothing with LOOCV bandwidth selection
all_galaxies = smooth_all_galaxies(all_galaxies)

# Generate main science plot: smoothed B-field vs. time since infall
plot_smoothed_ensemble(all_galaxies)

# Fit exponential growth model per galaxy and plot distributions of tau
fits = fit_all_and_plot_distributions(all_galaxies)
# Expected output: printed table of tau and B0 per galaxy,
# histogram of tau distribution, individual fit overlay plot
```

**Expected console output for `process_all_satellites`:**
```
[1/50] SubhaloID 12345 ... OK (infall z=0.42)
[2/50] SubhaloID 12367 ... OK (infall z=0.85)
[3/50] SubhaloID 12389 ... SKIPPED: 403 Client Error
...
Processed  : 47 galaxies
With infall: 43
```

---

## Results & Validation

**Current status:** Preliminary results based on a subsample of ~10–50 satellites of the most massive TNG100-1 halo (SubhaloGrNr = 0).

**What the results show so far:**
- The smoothed B-field tracks show a clear increasing trend after infall for most galaxies, consistent with McDonough & Poulin (2025)
- The LOOCV procedure successfully identifies optimal bandwidths in the range 0.1–2.0 Gyr, with the diagnostic plots confirming the smoother captures real trends rather than noise
- Individual exponential fits are possible for galaxies with sufficient post-infall coverage; the distribution of τ values will be the key science result once the full sample is analyzed

**Validation approach:**
- LOOCV itself serves as an internal validation: it measures how well the smoother predicts held-out data points
- Infall times are cross-checked by verifying that all z=0 satellites have Δt ≥ 0 (infall must be in the past)
- The log-space smoothing is validated by visual inspection of the diagnostic plots

---

## Known Limitations

- Results are based on a subsample (~10–50 galaxies) — the exponential fit distributions require N ≥ 30 for robust statistics
- Some subhalo IDs return 403 errors (no merger tree available) and are skipped automatically
- Analysis is restricted to `SubhaloGrNr = 0` (one host halo); future work will generalize to a broader sample
- The pre-infall (Δt < 0) regime is sparsely sampled in the current subsample

---

## References

- McDonough, B. & Poulin, A. (2025). *Magnetic Fields of Satellite Galaxies Stronger than Comparable Centrals in TNG100.* Research Notes of the AAS, 9, 93.
- IllustrisTNG Collaboration. [tng-project.org](https://www.tng-project.org)
- Astropy Collaboration (2022). *The Astropy Project.* ApJ, 935, 167.

---

## License

MIT License — see `LICENSE` file.

> **Note:** This project incorporates `iapi.py`, originally written by Bryanne McDonough for the TNGWorkshops and modified for this project. The TNG simulation data is subject to the [IllustrisTNG data usage policy](https://www.tng-project.org/data/usage/).

---

## AI Assistance Disclosure

This project used [Claude](https://claude.ai) (Anthropic) as a coding assistant throughout development. Specific uses included:

- Debugging TNG API queries and HTTP errors
- Structuring the analysis pipeline and notebook layout
- Learning and refining code cells (kernel smoothing, LOOCV, some plotting)
- Organization and documentation

All code in this repository has been initiated, reviewed, tested, and modified by the author. All scientific interpretations, methodological decisions, and conclusions are the author's own.
