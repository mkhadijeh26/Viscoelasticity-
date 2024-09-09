# Prony Series Fit for Viscoelastic Data

This project provides a Python-based implementation to fit viscoelastic data using the **Prony series**. The code uses experimental data (storage modulus and loss modulus) and optimizes the Prony series parameters to model the material's behavior.

## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): $E'(ω)$
- **Loss modulus** (viscous behavior): $E''(ω)$

The equations for the Prony series used in this project are as follows:

### Storage Modulus E'(ω):

![Storage Modulus](https://latex.codecogs.com/svg.image?E'(\omega)%20=%20E_0%20\left(1%20-%20\sum_{i=1}^{n}%20g_i%20+%20\sum_{i=1}^{n}%20g_i%20\frac{\omega^2%20\tau_i^2}{1%20+%20\omega^2%20\tau_i^2}%20\right))

### Loss Modulus E''(ω):

![Loss Modulus](https://latex.codecogs.com/svg.image?E''(\omega)%20=%20E_0%20\sum_{i=1}^{n}%20g_i%20\frac{\omega%20\tau_i}{1%20+%20\omega^2%20\tau_i^2})

Where:
- $E_₀$ is the long-term modulus.
- $g_ᵢ$ are the Prony series coefficients (describing the material's relaxation behavior).
- $τ_ᵢ$ are the relaxation times.
- $ω$ is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://latex.codecogs.com/svg.image?\text{MAPE}(E',%20E'')%20=%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}'%20-%20E_{\text{calc}}'}{E_{\text{exp}}'}%20\right|%20+%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}''%20-%20E_{\text{calc}}''}{E_{\text{exp}}''}%20\right|)

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of $NP$ candidate solutions $x_i$ ($i$ = 1, ..., $NP$) is randomly generated within the specified parameter bounds. Each $x_i$ represents a set of Prony series parameters $E_0$, $g_1$, $τ_1$, ..., $g_n$, $τ_n$.

2. **Mutation**: 
   For each target vector x_i, a mutant vector $v_i$ is created using the "best1bin" strategy:
   
   ![Mutation](https://latex.codecogs.com/svg.image?v_i%20=%20x_{best}%20+%20F%20\cdot%20(x_{r1}%20-%20x_{r2}))
   
   Where x_best is the best solution so far, $x_r1$ and $x_r2$ are two randomly chosen distinct vectors, and $F$ is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector $u_i$ is created by mixing components of $x_i$ and $v_i$:
   
   ![Crossover](https://latex.codecogs.com/svg.image?u_%7Bi%2Cj%7D%20%3D%20%5Cbegin%7Bcases%7D%20v_%7Bi%2Cj%7D%20%26%20%5Ctext%7Bif%20%7D%20%5Ctext%7Brand%7D(0%2C1)%20%5Cleq%20CR%20%5Ctext%7B%20or%20%7D%20j%20%3D%20j_%7Brand%7D%20%5C%5C%20x_%7Bi%2Cj%7D%20%26%20%5Ctext%7Botherwise%7D%20%5Cend%7Bcases%7D)
   
   Where $CR$ is the crossover rate (set to 0.7 in this implementation) and $j_{rand}$ is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector $u_i$ is evaluated using the $**MAPE**$ objective function. If it yields a lower MAPE than $x_i$, it replaces $x_i$ in the next generation:
   
   ![Selection](https://latex.codecogs.com/svg.image?x_i%5E%7BG%2B1%7D%20%3D%20%5Cbegin%7Bcases%7D%20u_i%5EG%20%26%20%5Ctext%7Bif%20%7D%20f(u_i%5EG)%20%3C%20f(x_i%5EG)%20%5C%5C%20x_i%5EG%20%26%20%5Ctext%7Botherwise%7D%20%5Cend%7Bcases%7D)
   
   Where $f()$ represents the $MAPE$ objective function and $G$ is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of 1e-7.

This process is repeated for different numbers of Prony terms (from 1 to 10 by default) to find the optimal number of terms that best fits the experimental data. The algorithm's adaptive nature allows it to efficiently explore the parameter space and converge to a global optimum, making it well-suited for fitting the non-linear Prony series model to complex viscoelastic data.

The differential evolution algorithm is particularly effective for this problem because:
- It can handle non-linear, non-convex optimization landscapes.
- It doesn't require gradient information, which is beneficial for the complex Prony series equations.
- It's less likely to get trapped in local optima compared to traditional gradient-based methods.
- It can efficiently handle the varying number of parameters as the number of Prony terms changes.

By employing this sophisticated optimization technique, the code can accurately fit the Prony series to a wide range of viscoelastic materials, providing reliable parameters for use in finite element simulations and other material modeling applications.
