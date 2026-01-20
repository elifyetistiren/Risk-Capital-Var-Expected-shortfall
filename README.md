# Financial Risk Engine: Market & Liquidity Risk

## Overview
This project is a comprehensive Financial Risk Engine designed to model **Market Risk** and **Liquidity Risk** for a multi-asset portfolio. It calculates industry-standard risk metrics including Value at Risk (VaR) and Expected Shortfall (ES) using three distinct methodologies, and integrates a liquidity cost model to produce Liquidity-Adjusted VaR (L-VaR).

## Key Features
- **Multi-Method Risk Calculation**: Calculates VaR and ES at a 99% confidence level using:
  - **Historical Simulation**: Based on actual past returns.
  - **Parametric (Normal) Method**: Based on volatility and covariance matrices.
  - **Monte Carlo Simulation**: Based on Geometric Brownian Motion (GBM) path generation.
- **Liquidity Risk Module**: Calculates "Liquidity Add-ons" to account for the cost of liquidation in stressed markets:
  - **Bid-Ask Spread Cost**: Adjusted for Time to Liquidation (TTL).
  - **Market Impact Cost**: Penalty based on position size and selling pressure.
- **Horizon Scaling**: Scales metrics for both 1-day and 10-day risk horizons.
- **Reporting**: Aggregates results across all methodologies for side-by-side comparison.

## Repository Structure

### Data Inputs
* `3assets_prices.csv`: Historical daily price data for the portfolio assets (Asset A, B, C).
* `3assets_returns.csv`: Pre-calculated daily log returns derived from the price data.

### Core Risk Models
* `Historic_Var_ES.ipynb`: implementation of Historical Simulation for VaR and ES. Uses empirical percentiles from the return distribution.
* `Normal_Var_ES.ipynb`: Implementation of the Variance-Covariance (Parametric) method. Assumes normal distribution of returns.
* `Monte_Carlo_VaR_ES.ipynb`: Simulates thousands of future price paths to estimate tail risk.

### Liquidity & Aggregation
* `Liquidity.ipynb` / `updatedLiquidityMetrics.ipynb`: Calculates specific liquidity costs (Spread + Impact) and adds them to the Base VaR to derive L-VaR.
* `combined_metrics.ipynb`: The primary reporting engine. Reads outputs from the Historical, Normal, and Monte Carlo notebooks to generate a master comparison table.
* `MarketRisk_Graphs.ipynb`: Visualization suite generating correlation heatmaps, return histograms, and liquidity waterfalls.

## Usage

1.  **Prerequisites**: Ensure you have Python installed with the following libraries:
    ```bash
    pip install pandas numpy matplotlib seaborn scipy
    ```

2.  **Data Setup**: Ensure `3assets_prices.csv` and `3assets_returns.csv` are in the root directory.

3.  **Execution Order**:
    It is recommended to run the notebooks in the following order to ensure dependencies are met:
    1.  `Normal_Var_ES.ipynb` (Establishes baseline parametric risk)
    2.  `Historic_Var_ES.ipynb`
    3.  `Monte_Carlo_VaR_ES.ipynb`
    4.  `updatedLiquidityMetrics.ipynb` (Calculates add-ons)
    5.  `combined_metrics.ipynb` (Aggregates all results)

## Methodologies Detail

### Liquidity Adjusted VaR (L-VaR) Formula
The model calculates L-VaR by adding liquidity costs to the base Market Risk VaR:
$$ L\text{-}VaR = \text{Base VaR} + (\text{Spread Cost} \times \sqrt{TTL}) + \text{Market Impact} $$

### Monte Carlo Engine
- **Simulations**: Generates random paths using the Cholesky decomposition of the asset covariance matrix.
- **Time Steps**: Simulates daily steps projected out to 10 days to capture non-linear path dependencies.
