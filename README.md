# Numerical_methods_Project
This repository will be used to complete my work on my computational project for PHY 225-001. 

# **Project Title**

Exploring the Timescale Dependence of Magnetic Field Growth in Satellite Galaxies


# **Project Description**

This project investigates how magnetic fields grow over time in satellite galaxies using data from the IllustrisTNG cosmological simulation. Previous work has shown that satellite galaxies exhibit stronger magnetic fields than central galaxies at fixed stellar mass, but the timescale over which this amplification occurs remains unclear. The goal of this project is to quantify how magnetic field strength evolves before and after a galaxy becomes a satellite.

To address this, I will reconstruct galaxy evolutionary histories using merger tree data to identify the moment of central-to-satellite transition. I will then track magnetic field strength as a function of redshift (time) and measure how it changes relative to the time of accretion. The project will apply numerical methods such as interpolation, time-series analysis, and averaging to handle discrete simulation outputs and extract meaningful trends. By analyzing magnetic field growth as a function of time since infall, this work aims to constrain the amplification timescale and better understand environmental effects in galaxy evolution.



# **Planned Repository Structure**

```
project-root/
│
├── data/
│   ├── raw/
│   ├── processed/
│
├── notebooks/
│   ├── data_exploration.ipynb
│   ├── magnetic_field_analysis.ipynb
│
├── src/
│   ├── load_data.py
│   ├── merger_tree.py
│   ├── analysis.py
│   ├── plotting.py
│
├── results/
│   ├── figures/
│   ├── outputs/
│
├── README.md
└── requirements.txt
```



# **Resources**

* IllustrisTNG simulation dataset
* TNG API documentation and example scripts
* Workshop notebooks provided in class
* Astropy (cosmological time conversions)
* NumPy and SciPy (numerical methods and data processing)
* Matplotlib (visualization)
* Research literature on magnetic field amplification in satellite galaxies



# **Project Plan and Timeline**

**Week 1 (Proposal Stage)**

* Finalize project scope and repository setup
* Review workshop notebooks and TNG data structure

**Week 2**

* Load satellite galaxy data and identify relevant fields
* Extract magnetic field and time (scale factor/redshift) information

**Week 3**

* Convert scale factor to redshift and lookback time
* Plot magnetic field as a function of redshift for a single galaxy

**Week 4**

* Generalize code into reusable functions
* Apply analysis to multiple satellite galaxies

**Week 5**

* Implement numerical methods:

  * Interpolation of magnetic field evolution
  * Averaging or smoothing noisy data
* Begin comparing trends across galaxies

**Week 6**

* Incorporate merger tree data
* Identify satellite infall times and align time axes

**Final Week**

* Analyze magnetic field growth relative to infall time
* Generate final plots and interpret results
* Clean and document code



# **Reach Goals**

* Quantify magnetic field growth rates before and after infall
* Compare satellites with different masses or environments
* Fit a model to describe magnetic field amplification timescales


# **Additional Notes**

This project connects numerical methods with an active research problem in astrophysics. A key challenge will be working with discrete simulation outputs and reconstructing continuous time evolution, which will require careful use of interpolation and data handling techniques. The final result will be a reproducible computational pipeline for analyzing magnetic field evolution in simulated galaxies.
