# Prony Series Fit for Viscoelastic Data

This project provides a Python-based implementation to fit viscoelastic data using the **Prony series**. The code uses experimental data (storage modulus and loss modulus) and optimizes the Prony series parameters to model the material's behavior. This is useful in modeling viscoelastic materials, such as polymers, by fitting their frequency-dependent storage and loss moduli.

## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): E'(ω)
- **Loss modulus** (viscous behavior): E''(ω)

The equations for the Prony series used in this project are as follows:

### Storage Modulus E'(ω):

![Storage Modulus](https://latex.codecogs.com/svg.latex?E'(\omega)%20=%20E_0%20\left(1%20-%20\sum_{i=1}^{n}%20g_i%20+%20\sum_{i=1}^{n}%20g_i%20\frac{\omega^2%20\tau_i^2}{1%20+%20\omega^2%20\tau_i^2}%20\right))

### Loss Modulus E''(ω):

![Loss Modulus](https://latex.codecogs.com/svg.latex?E''(\omega)%20=%20E_0%20\sum_{i=1}^{n}%20g_i%20\frac{\omega%20\tau_i}{1%20+%20\omega^2%20\tau_i^2})

Where:
- E₀ is the long-term modulus.
- gᵢ are the Prony series coefficients (describing the material's relaxation behavior).
- τᵢ are the relaxation times.
- ω is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://latex.codecogs.com/svg.latex?\text{MAPE}(E',%20E'')%20=%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}'%20-%20E_{\text{calc}}'}{E_{\text{exp}}'}%20\right|%20+%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}''%20-%20E_{\text{calc}}''}{E_{\text{exp}}''}%20\right|)

The **differential evolution** algorithm works as follows:
1. **Initialization**: A population of candidate solutions is randomly generated.
2. **Mutation and Crossover**: New candidate solutions are generated based on mutations of the best solutions.
3. **Selection**: Candidate solutions that result in a lower objective function value are selected.
4. **Stopping Criteria**: The algorithm runs for a specified number of iterations or until convergence.

## Adaptive Fitting

The script adapts the number of terms in the Prony series, ranging from 1 to 5 terms, to achieve the best fit. The code incrementally increases the number of terms and chooses the model with the lowest MAPE for both the storage and loss moduli.

## Visualization

The code also generates plots comparing the experimental data with the fitted Prony series for both the storage and loss moduli.

## How to Run the Code

1. **Requirements**:
   - Python 3.x
   - NumPy
   - SciPy
   - Pandas
   - Matplotlib

2. **Data**:
   Provide an Excel file with columns for **Frequency**, **Storage Modulus**, and **Loss Modulus**.

3. **Run the Script**:
   Simply run the script with your experimental data. The output will show the optimized Prony series parameters and a plot of the fit compared to the experimental data.

4. **Example Command**:
   ```bash
   python prony_fit.py
   ```
