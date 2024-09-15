# To cite:
[![DOI](https://zenodo.org/badge/854498798.svg)](https://zenodo.org/doi/10.5281/zenodo.13735907)



# Prony Series Fit for Viscoelastic Data

This project provides a Python-based implementation to fit viscoelastic data using the **Prony series**. The code uses experimental data (storage modulus and loss modulus) and optimizes the Prony series parameters to model the material's behavior. This is useful in modeling viscoelastic materials, such as polymers, by fitting their frequency-dependent storage and loss moduli. The resulting Prony series parameters are particularly useful for creating material models in finite element software like ABAQUS.

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

The script adapts the number of terms in the Prony series, ranging from 1 to a user-defined maximum number of terms (default is 10). The code incrementally increases the number of terms and chooses the model with the lowest MAPE for both the storage and loss moduli. This adaptive approach ensures that the optimal number of Prony terms is used for each dataset.

## Visualization

The code generates plots comparing the experimental data with the fitted Prony series for both the storage and loss moduli. These plots are displayed using a logarithmic scale for both axes, providing a clear visualization of the fit across the entire frequency range.

## How to Run the Code

1. **Requirements**:
   - Python 3.x
   - NumPy
   - SciPy
   - Pandas
   - Matplotlib

2. **Data**:
   Provide an Excel file with columns for **Frequency**, **Storage-Modulus**, and **Loss-Modulus**.

3. **Run the Script**:
   Ensure that the file path in the `main()` function is correct for your system. Then run the script using Python. The output will show the optimized Prony series parameters, the number of terms used, and a plot of the fit compared to the experimental data.

4. **Example Command**:
   ```bash
   python prony_fit.py
   ```

## Output

The script provides detailed output, including:
- MAPE values for each number of Prony terms tested
- The optimal number of Prony terms
- The long-term modulus (E0)
- Individual Prony series parameters (gi and τi)
- The sum of gi values
- A plot comparing experimental and fitted data

## Applications

The Prony series parameters obtained from this script are particularly useful for creating material models in finite element software like ABAQUS. These parameters can be directly input into ABAQUS to define viscoelastic material behavior, enabling accurate simulation of time-dependent material responses.

## Customization

You can customize the maximum number of Prony terms by modifying the `No_Prony` variable at the beginning of the script. This allows you to adjust the complexity of the fit based on your specific material and data requirements.

