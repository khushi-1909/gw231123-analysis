# GW231123 Analysis

End-to-end gravitational-wave data analysis of **GW231123_135430**, a short-duration, high-mass binary black hole merger reported by the LIGO–Virgo–KAGRA Collaboration (Abac et al. 2025).

Project for the **Observational Gravitational Wave Astronomy** module of the MSc Data-Intensive Astrophysics at Cardiff University.

---

## What this notebook does

Using the open strain data from the LIGO Hanford (H1) and Livingston (L1) detectors, this analysis covers the full GW analysis pipeline:

### 1. Data and noise model
- Fetches strain data from the [Gravitational Wave Open Science Center (GWOSC)](https://gwosc.org) using `gwpy`.
- Estimates the **Power Spectral Density (PSD)** of detector noise using Welch averaging with a Tukey window (4 s segments, 2 s overlap).
- **Whitens** the strain data using the PSD and applies a band-pass filter (broad 20–256 Hz, and a narrower 30–80 Hz cut motivated by the published signal band).

### 2. Signal modelling
- Generates a **literature-motivated CBC waveform** with `pycbc.waveform`, using the source-frame masses, spins, and redshift from Abac et al. (2025).
- Generates a **phenomenological sine-Gaussian burst model**, parameterised by central frequency `f₀` and quality factor `Q`.
- Compares CBC vs burst morphology by eye in the time domain.

### 3. Detection (coincident analysis)
- Computes the **matched-filter SNR time series** for both the CBC and burst templates in each detector separately.
- Combines H1 and L1 peak SNRs into a coincident ranking statistic.
- Estimates a bound on the **False Alarm Rate (FAR)** using a **time-slide background** over an extended off-source segment (10,000 artificial shifts), yielding a FAR bound of ~9.8 × 10⁻⁸ Hz and an IFAR > 10⁷ s.

### 4. Parameter estimation (coherent analysis)
- Builds a frequency-domain Gaussian likelihood, prior, and posterior over a reduced 5D parameter set for the CBC model (H1 time offset, H1–L1 delay, amplitude scale, amplitude ratio, common phase), and a smaller 4D set for the burst model.
- Localises the posterior by **multi-start optimisation** of the log-posterior.
- Profiles the posterior around the best-fit point along each parameter.
- Samples the full posterior using the **emcee** ensemble MCMC sampler (burn-in + production phases).
- Visualises results with **trace plots and corner plots**.
- Compares the CBC and burst fits: the burst posterior is numerically cleaner and gives a more physically plausible H1–L1 timing delay than the reduced CBC fit, which pins several parameters to prior boundaries — a sign that the reduced model is too restrictive.

---

## Key results

- **CBC matched-filter SNR:** ~9.98 (H1), ~11.32 (L1); coincident ~15.1.
- **Burst matched-filter SNR:** ~11.27 (H1), ~12.02 (L1).
- **FAR bound:** ~9.8 × 10⁻⁸ Hz, IFAR > 10⁷ s.
- The observed coincident statistic lies well above the time-slide background.

Results are broadly consistent with the published analysis (Abac et al. 2025), with the differences traceable to the simplifications used in this notebook (reduced parameter set, fixed intrinsic waveform parameters, single waveform model, coincident-only detection statistic).

---

## How to run

This notebook uses public data and standard GW Python tooling. It is designed to run in Google Colab or any local environment with Python 3.10+.

```bash
pip install gwosc gwpy pycbc lalsuite emcee corner numpy matplotlib scipy
```

Then open `GW231123_analysis.ipynb` and run all cells. The data is fetched on the fly from GWOSC, so an internet connection is required.

---

## Tools and libraries

- **GW data access:** `gwosc`, `gwpy`
- **Waveform generation:** `pycbc.waveform`, `lalsuite` (IMRPhenomXPHM)
- **MCMC:** `emcee`
- **Visualisation:** `matplotlib`, `corner`
- **Numerics:** `numpy`, `scipy`

---

## References

1. Abac, A. G., et al. (LIGO–Virgo–KAGRA Collaboration). 2025, *The Astrophysical Journal Letters*, **993**, L25. *GW231123: A Binary Black Hole Merger with Total Mass 190–265 M⊙*. DOI: [10.3847/2041-8213/ae0c9c](https://doi.org/10.3847/2041-8213/ae0c9c).
2. Abbott, B. P., et al. (LIGO–Virgo). 2016, *Phys. Rev. Lett.*, **116**, 061102. *Observation of Gravitational Waves from a Binary Black Hole Merger*.
3. Abbott, B. P., et al. (LIGO–Virgo). 2017, *Phys. Rev. Lett.*, **119**, 161101. *GW170817: Observation of Gravitational Waves from a Binary Neutron Star Inspiral*.

---

## Author

**Khushi Verma**
MSc Data-Intensive Astrophysics, Cardiff University
[LinkedIn](https://www.linkedin.com/in/khushi-verma-1a44a722b/) · [GitHub](https://github.com/khushi-1909)
