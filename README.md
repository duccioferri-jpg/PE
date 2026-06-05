# Private Equity Secondary Market Simulator: The Reverted J-Curve & Discount Estimator

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![NumPy](https://img.shields.io/badge/NumPy-Data%20Science-orange)
![SciPy](https://img.shields.io/badge/SciPy-Statistics-lightblue)
![Matplotlib](https://img.shields.io/badge/Matplotlib-Visualization-yellow)

## 📌 Project Overview
This repository contains a stochastic simulation engine (Monte Carlo) developed to analyze and compare the performance and risk dynamics between the **Primary** and **Secondary** Private Equity markets. 

The model mathematically demonstrates the elimination of the traditional J-Curve (the "Reverted J-Curve"), the drastic mitigation of *Blind Pool Risk*, and introduces a novel, closed-loop validated algebraic estimator to deduce private transaction discounts from public cash flows.

This codebase was developed as an integral part of my academic thesis and research work.

## ✨ Core Features & Methodology
- **Stochastic Asset Generation:** Simulates the evolution of underlying portfolio companies' NAVs via Geometric Brownian Motion (GBM).
- **Appraisal Smoothing (BGG 2023):** Implements a realistic accounting lag to simulate the pricing friction between the "True NAV" and the "Reported NAV" published quarterly by General Partners (GPs).
- **Dual Time-Alignment:** Calculates performance metrics by aligning cash flows along two different time axes: *Calendar Time* (overall fund life) and *Investor Age* (the individual holding period of the buyer).
- **Vectorized Computation:** Utilizes the bisection method via NumPy to solve for the IRR across tens of thousands of funds simultaneously without slow iterative loops.
- **Relative Metrics:** Includes the calculation of the **KS-PME (Kaplan-Schoar Public Market Equivalent)** to adjust performance for public equity market beta and isolate managerial alpha.

## 🗂️ Model Structure

The simulation is divided into 5 sequential logical steps:

### Step 1 & 2: Universe Creation & Accounting
Generates a Monte Carlo sample of N funds (e.g., 10,000). The model builds portfolios company by company and applies parametric smoothing to create the delayed, reported accounting valuations visible to limited partners.

### Step 3: Secondary Entry & Metrics Calculation
Simulates the entry of a secondary buyer (LP) at the fund's mid-life. The buyer purchases the LP stake by applying a pre-negotiated discount to the *Reported NAV*. The model calculates the IRR, TVPI, DPI, and KS-PME using time-aligned cash flows.

### Step 4: Data Visualization & Risk Analysis
Generates performance plots (The Reverted J-Curve). It utilizes percentile bands (Interquartile Range or P10-P90) to provide visual proof of the drastic reduction in return dispersion (risk) in the secondary market compared to the primary market.

### Step 5: Closed-Loop Validation (The Implied Discount Estimator)
Mathematically validates an inverse algebraic formula designed to recover the original, private transaction discount ($d$) relying purely on observable terminal performance multiples:
```math
d = (c + 1) \times \left(1 - \frac{TVPI_P}{TVPI_S}\right)
