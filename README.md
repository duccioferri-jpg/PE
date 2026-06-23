# Endogenous Secondary Pricing Model (ESPM)

## Executive Summary
This project features a Python-based stochastic Monte Carlo simulation engine designed to evaluate the financial performance and structural dynamics of Primary versus Secondary Private Equity funds. Built with the rigorous, quantitative mindset of an Aerospace Engineer applied to Industrial Business Management, this model bridges the gap between theoretical data and real-world business performance. 

By simulating portfolio companies through Geometric Brownian Motion (GBM) and passing cash flows through exact institutional fee structures (European Waterfall), the engine isolates the mechanical drivers of the Private Equity "J-Curve" and evaluates the true economic impact of secondary market discounts.

---

## Model Architecture & Methodology

The simulation engine is housed in `ESPM.ipynb` and is meticulously structured into five sequential modules:

### Step 1: Macro-Calibration & Parametrization
This section initializes the simulation environment and bounds it to empirical macroeconomic realities.
*   **Micro-Dynamics:** Portfolio companies are modeled using a Geometric Brownian Motion (GBM). Sector-specific drifts ($\mu$), idiosyncratic volatilities ($\sigma$), and Hamada-relevered betas are anchored to Damodaran's industry datasets. The $-\frac{1}{2}\sigma^2$ term in the GBM naturally tempers arithmetic returns in high-dispersion sectors, resulting in a realistically conservative aggregate TVPI.
*   **Accounting Lag:** The GP appraisal smoothing parameter is strictly calibrated to $\lambda=0.52$ to accurately simulate the stale reporting of True NAV inherent in Private Equity accounting.
*   **Exogenous Distributions:** Baseline secondary entry timing and discount depths are drawn from empirical academic distributions, which the model later endogenizes.

### Step 2: The Simulation Engine (Systemic Macro-Cohorts)
Built to accurately capture systemic risk and avoid artificially narrow confidence intervals.
*   **Flattened Cohort Architecture:** Instead of simulating 10,000 independent funds, the model generates 50 independent global macro-economies, clustering 200 funds within each to preserve systemic correlation.
*   **Institutional Mechanics:** Cash flows are processed through an exact Sequential European Waterfall, accounting for an 8% preferred return (hurdle), a 100% GP Catch-Up, a 20% carried interest, and a 2% management fee that steps down from Committed to Active Capital after the investment period.
*   **Endogenous Secondary Entry:** Sale timing is dynamically coupled to the macro-regime. During simulated market crashes (1-year trailing index drop > 10%), LP distress surges via the Denominator Effect. This mechanically clusters funds into the deep-discount "Liquidity Channel," overriding standard probabilistic entry.

### Step 3: Performance Metrics & Empirical Validation
*   **Industry Standards:** The model calculates standard Private Equity performance metrics (Net IRR, Net TVPI, DPI, and Kaplan-Schoar PME) for both primary and secondary trajectories.
*   **Moment-Matching:** The notebook validates that the micro-parameters organically reproduce non-targeted macroeconomic realities (e.g., net TVPI ~1.40x, KS-PME ~1.0x). It utilizes Direct Alpha (Gredil-Griffiths-Stucke) to ensure gross and net alpha remain within literature-supported brackets.
*   **The Reverted J-Curve:** The aggregated data demonstrates how secondary entry mechanically mitigates early-stage fee drag and accelerates the DPI trajectory.

### Step 4: Algebraic Sanity Check (Closed-Loop Validation)
Before applying the TVPI-gap estimator to noisy, out-of-sample data, the model tests the theoretical algebraic inversion under ideal conditions.
*   A deterministic grid of discounts (0% to 30%) is forced onto predetermined entry ages.
*   The estimator successfully plots a 45-degree diagonal recovery line, proving that the mathematical formula correctly reverses the cash-flow mechanics, carrying only a marginal offset due to embedded fee drag.

### Step 5: Discount Recovery & The Staleness Wedge (Headline Finding)
This final step deploys the estimator on the empirical, out-of-sample secondary funds generated in Step 2.
*   **Cluster Robust Bootstrap:** To compute honest confidence intervals, the bootstrap resamples entire macro-economies rather than individual funds, preserving systemic correlation.
*   **Quality Alpha & Selection Bias:** By calculating the recovery within each of the 50 economies, the model differences out the macro draw, significantly tightening the confidence intervals and revealing the impact of Selection Bias in shallow, opportunistic discount channels.
*   **The Staleness Wedge:** The model decomposes the observable contract discount against the True Economic Discount. The output proves that during forced-selling regimes (market crashes), the observable discount severely overstates the true liquidity premium due to General Partner NAV staleness.

---

## Instructions: How to Run the Model

**Prerequisites:**
The simulation is built in Python and requires a standard data science environment (e.g., Jupyter Notebook or JupyterLab). Ensure you have the following libraries installed:
*   `numpy`
*   `pandas`

**Execution:**
1. Clone or download the repository to your local machine.
2. Open the `ESPM.ipynb` notebook.
3. Execute the cells sequentially from top to bottom. 
4. **Note on Compute Time:** The engine processes 10,000 Monte Carlo iterations across a 40-quarter (10-year) grid. Due to the complex macro-cohort architecture and sequential European waterfall calculations, a standard run will take approximately 30-40 seconds depending on your hardware.
