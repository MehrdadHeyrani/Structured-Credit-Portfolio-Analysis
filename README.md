# STRUCTURED CREDIT PORTFOLIO ANALYSIS — TRADE FINANCE ENGINE

## Liquidity Management & Credit Risk Assessment Framework

**Version 2.0**

---

##  Overview

This project provides a robust and comprehensive framework for analyzing trade finance portfolios, focusing on liquidity management and credit risk assessment. Built for financial institutions, it offers a suite of tools ranging from synthetic data generation to advanced risk simulations and publication-quality visualizations.

### Key Features:

*   **Synthetic Portfolio Generation**: Creates high-volume (20,000 records) and realistic trade finance invoice datasets.
*   **Structured Credit Engine**: Implements borrowing base calculation with sophisticated eligibility filters, concentration haircuts, and advance rate application.
*   **Stress Testing**: Simulates systemic payment delay scenarios to assess liquidity sensitivity.
*   **Monte Carlo Simulation**: Quantifies probabilistic default risk and computes key risk metrics like Value at Risk (VaR) and Conditional Value at Risk (CVaR).
*   **Visual Dashboard**: Generates a 6-panel, publication-quality dashboard for intuitive risk insights.
*   **Executive Summary Report**: Provides a detailed console output summarizing portfolio characteristics, stress test results, and Monte Carlo findings.

## Dependencies

To run this project, you need the following Python libraries. You can install them using pip:

```bash
pip install pandas numpy matplotlib seaborn scipy
```

##  Usage

To execute the full analysis, simply run the main Python script. This will generate a synthetic portfolio, perform all analyses, print an executive summary to the console, and save two plots: `trade_finance_dashboard.png` and `correlation_heatmap.png`.

```bash
python trade_finance_analysis.py
```

## Project Structure

The core logic is encapsulated within a single Python script (`trade_finance_analysis.py` - or the equivalent in your notebook). It's logically divided into the following sections:

1.  **SECTION 1 — DATA GENERATION**: Contains the `generate_institutional_portfolio` function for creating synthetic data.
2.  **SECTION 2 — STRUCTURED CREDIT ENGINE**: Houses the `EngineConfig` dataclass and the `StructuredCreditEngine` class, which includes methods for `compute_borrowing_base`, `run_stress_test`, `run_monte_carlo`, and `compute_risk_metrics`.
3.  **SECTION 3 — VISUALISATION DASHBOARD**: Defines the `plot_dashboard` function, responsible for generating the multi-panel risk visualization.
4.  **SECTION 4 — CORRELATION HEATMAP**: Includes `plot_correlation_heatmap` for visualizing numerical relationships within the portfolio.
5.  **SECTION 5 — EXECUTIVE SUMMARY REPORT**: Contains `print_executive_summary` for generating the console-based textual report.
6.  **MAIN — ORCHESTRATION**: The `main` function ties all components together, running the analysis pipeline sequentially.

##  Configuration

The `EngineConfig` dataclass within `SECTION 2` allows for easy modification of key parameters that govern the credit facility and risk simulations:

```python
@dataclass
class EngineConfig:
    facility_limit:      float = 150_000_000.0   # Max facility: $150M
    advance_rate:        float = 0.85              # 85% advance on net eligible
    concentration_cap:   float = 0.10              # Max 10% per debtor
    max_days_eligible:   int   = 90                # Invoices aged ≤90 days are eligible
    monte_carlo_iters:   int   = 1_000             # MC simulation iterations
    default_probability: float = 0.03              # 3% idiosyncratic default prob
    stress_max_delay:    int   = 30                # Max delay days in stress test
    stress_step:         int   = 5                 # Step size for stress delay loop
```

Adjust these parameters in the `main` function's `EngineConfig` instantiation to explore different scenarios or facility structures.

##  Outputs

Upon execution, the script generates:

*   **Executive Summary Report**: A comprehensive textual report printed directly to the console with key metrics and insights.
*   **`trade_finance_dashboard.png`**: A detailed, multi-panel visual summary of the portfolio's risk and liquidity.

    <img src="dash1.png" alt="" width="1500" align="center"/>

*   **Executive Summary Report**: A comprehensive textual report printed directly to the console with key metrics and insights.

```
══════════════════════════════════════════════════════════════════════════
  STRUCTURED CREDIT PORTFOLIO  ·  EXECUTIVE RISK REPORT
══════════════════════════════════════════════════════════════════════════

  ① PORTFOLIO OVERVIEW
  ──────────────────────────────────────────────────────────────────────────
  Total Records       :       20,000
  Gross Portfolio ($) :     179,438,281
  Disputed Invoices   :          312  (1.56%)
  Unique Debtors      :          150
  Avg Invoice Amount  : $       8,972

  ② BORROWING BASE (BASELINE)
  ──────────────────────────────────────────────────────────────────────────
  Eligible Invoices   :   14,824 / 20,000  (74.1%)
  Gross Eligible ($)  :     132,501,407
  Concentration HC($) :               0  (0.0% of gross)
  Net Eligible ($)    :     132,501,407
  BB Before Cap ($)   :     112,626,196
  Final Borrowing Base: $   112,626,196  (75.1% utilisation)

  ③ STRESS TEST RESULTS  (Systemic Payment Delay)
  ──────────────────────────────────────────────────────────────────────────
  Delay (Days)     Borrowing Base ($)     % of Baseline
  0                $       112,626,196    100.0%
  5                $       106,499,075     94.6%
  10               $       100,250,735     89.0%
  15               $        94,115,516     83.6%
  20               $        88,133,579     78.3%
  25               $        82,693,255     73.4%
  30               $        76,229,951     67.7%

  ④ MONTE CARLO  —  DEFAULT RISK METRICS  (1,000 iterations)
  ──────────────────────────────────────────────────────────────────────────
  Mean BB             : $    109,254,741
  Std Dev             : $        201,376  (0.2% CoV)
  5% Value-at-Risk    : $    108,925,507
  1% Value-at-Risk    : $    108,806,833
  5% CVaR (Tail Loss) : $    108,857,231
  Min (Worst Case)    : $    108,659,701
  Max (Best Case)     : $    109,843,525
  Skewness            :           0.0576
  Excess Kurtosis     :          -0.2030

  ⑤ CONCENTRATION RISK  —  TOP 5 DEBTORS
  ──────────────────────────────────────────────────────────────────────────
  Debtor_087       $ 1,522,436  ( 0.8%)  █
  Debtor_069       $ 1,498,601  ( 0.8%)  █
  Debtor_017       $ 1,487,869  ( 0.8%)  █
  Debtor_149       $ 1,473,978  ( 0.8%)  █
  Debtor_010       $ 1,454,446  ( 0.8%)  █

  ⑥ ANALYSIS NOTES
  ──────────────────────────────────────────────────────────────────────────
  • Invoices >90 days outstanding are ineligible per the Borrowing Base Agreement.
  • Concentration haircuts apply to debtors exceeding 10% of eligible pool.
  • Monte Carlo default probability: 3% per invoice (binomial, idiosyncratic).
  • Stress test applies uniform delay shift to all days_outstanding values.
  • 5% VaR represents the 1-in-20 adverse borrowing base scenario.
```
