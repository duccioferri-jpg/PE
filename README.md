##  Detailed Step-by-Step Code Explanation

The simulation engine is housed in the `Primary_VS_Secondary_Funds.ipynb` notebook and is meticulously structured into five sequential steps. Below is a detailed breakdown of the internal mechanics at each stage of the model.

### STEP 1: Macro-Calibration & Parametrization
This section initializes the simulation environment and defines the global parameters.
* **Micro-Dynamics:** Portfolio companies are modeled using a Geometric Brownian Motion (GBM). Sector-specific drifts ($\mu$), idiosyncratic volatilities ($\sigma$), and Hamada-relevered betas are anchored to Damodaran's industry datasets. Note: The $-\frac{1}{2}\sigma^2$ term in the GBM naturally tempers arithmetic returns in high-dispersion sectors, resulting in a realistically conservative aggregate TVPI.
* **Accounting Lag:** The GP appraisal smoothing parameter is strictly calibrated to $\lambda = 0.50$ (Brown, Ghysels & Gredil, 2023) to simulate the stale reporting of True NAV.
* **Exogenous Distributions:** Baseline secondary entry timing and discount depths are drawn from empirical distributions (Nadauld et al., 2019), which are later endogenized in Step 2.

### STEP 2: The Simulation Engine (Systemic Macro-Cohorts)
To accurately model systemic risk and avoid artificially narrow confidence intervals, the engine employs a **Flattened Cohort Architecture**.
* **Macro Clustering:** Instead of simulating 10,000 independent funds, the model generates 50 independent global macro-economies, clustering 200 funds within each.
* **European Waterfall:** Cash flows are processed through an exact Sequential European Waterfall, including a 100% GP Catch-Up and a 2% management fee that steps down from Committed to Active Capital after the investment period.
* **Endogenous Secondary Entry:** Sale timing is dynamically coupled to the macro-regime. During simulated market crashes (1-year trailing index drop > 10%), LP distress surges via the *Denominator Effect*. This mechanically clusters funds into the deep-discount "Liquidity Channel," overriding standard probabilistic entry.

### STEP 3: Performance Metrics & Validation
The model calculates standard Private Equity performance metrics (Net IRR, Net TVPI, and Kaplan-Schoar PME) for both primary and secondary trajectories. 
* **Moment-Matching:** The notebook validates that the micro-parameters organically reproduce non-targeted macroeconomic realities (e.g., net TVPI ~1.40x, KS-PME ~1.0x, and blended secondary discounts ~13.8%). 
* **The Reverted J-Curve:** The aggregated data demonstrates how secondary entry mechanically mitigates early-stage fee drag and accelerates the DPI trajectory.

### STEP 4: Algebraic Sanity Check (Closed-Loop Validation)
Before applying the TVPI-gap estimator to noisy, out-of-sample data, the model tests the theoretical algebraic inversion under ideal conditions.
* A deterministic grid of discounts (0% to 30%) is forced onto predetermined entry ages (Years 3, 5, and 7).
* The estimator successfully plots a 45-degree diagonal recovery line, proving that the mathematical formula correctly reverses the cash-flow mechanics, carrying only a marginal 2-3pp offset due to embedded fee drag.

### STEP 5: Discount Recovery & The Staleness Wedge
This final step tests the estimator on the empirical, out-of-sample secondary funds generated in Step 2.
* **Cluster Robust Bootstrap:** To compute honest confidence intervals, the bootstrap resamples *entire macro-economies* rather than individual funds, preserving systemic correlation.
* **Within-Economy Fixed Effects:** By calculating the recovery within each of the 50 economies, the model differences out the macro draw, significantly tightening the confidence intervals and revealing the impact of **Selection Bias** (Quality Alpha) in shallow, opportunistic discount channels.
* **The Staleness Wedge (Headline Finding):** The model decomposes the observable contract discount against the True Economic Discount. The output proves that during forced-selling regimes (crashes), the observable discount severely overstates the true liquidity premium due to NAV staleness.
