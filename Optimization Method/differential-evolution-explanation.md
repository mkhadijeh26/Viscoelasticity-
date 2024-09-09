# Prony Series Fit for Viscoelastic Data

This project provides a Python-based implementation to fit viscoelastic data using the **Prony series**. The code uses experimental data (storage modulus and loss modulus) and optimizes the Prony series parameters to model the material's behavior.

## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): E'(ω)
- **Loss modulus** (viscous behavior): E''(ω)

The equations for the Prony series used in this project are as follows:

### Storage Modulus E'(ω):

![Storage Modulus](https://latex.codecogs.com/svg.image?E'(\omega)%20=%20E_0%20\left(1%20-%20\sum_{i=1}^{n}%20g_i%20+%20\sum_{i=1}^{n}%20g_i%20\frac{\omega^2%20\tau_i^2}{1%20+%20\omega^2%20\tau_i^2}%20\right))

### Loss Modulus E''(ω):

![Loss Modulus](https://latex.codecogs.com/svg.image?E''(\omega)%20=%20E_0%20\sum_{i=1}^{n}%20g_i%20\frac{\omega%20\tau_i}{1%20+%20\omega^2%20\tau_i^2})

Where:
- E₀ is the long-term modulus.
- gᵢ are the Prony series coefficients (describing the material's relaxation behavior).
- τᵢ are the relaxation times.
- ω is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://latex.codecogs.com/svg.image?\text{MAPE}(E',%20E'')%20=%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}'%20-%20E_{\text{calc}}'}{E_{\text{exp}}'}%20\right|%20+%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}''%20-%20E_{\text{calc}}''}{E_{\text{exp}}''}%20\right|)

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of NP candidate solutions x_i (i = 1, ..., NP) is randomly generated within the specified parameter bounds. Each x_i represents a set of Prony series parameters E_0, g_1, τ_1, ..., g_n, τ_n.

2. **Mutation**: 
   For each target vector x_i, a mutant vector v_i is created using the "best1bin" strategy:
   
   ![Mutation](https://latex.codecogs.com/svg.image?v_i%20=%20x_{best}%20+%20F%20\cdot%20(x_{r1}%20-%20x_{r2}))
   
   Where x_best is the best solution so far, x_r1 and x_r2 are two randomly chosen distinct vectors, and F is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector u_i is created by mixing components of x_i and v_i:
   
   ![Crossover](https://latex.codecogs.com/svg.image?u_{i,j}%20=%20\begin{cases}%20v_{i,j}%20&%20\text{if%20}%20\text{rand}(0,1)%20\leq%20CR%20\text{%20or%20}%20j%20=%20j_{rand}%20\\%20x_{i,j}%20&%20\text{otherwise}%20\end{cases})
   
   Where CR is the crossover rate (set to 0.7 in this implementation) and j_rand is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector u_i is evaluated using the MAPE objective function. If it yields a lower MAPE than x_i, it replaces x_i in the next generation:
   
   ![Selection](https://latex.codecogs.com/svg.image?x_i^{G+1}%20=%20\begin{cases}%20u_i^G%20&%20\text{if%20}%20f(u_i^G)%20<%20f(x_i^G)%20\\%20x_i^G%20&%20\text{otherwise}%20\end{cases})
   
   Where f() represents the MAPE objective function and G is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of 1e-7.

[The rest of the content remains the same...]
