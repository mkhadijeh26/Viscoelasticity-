# Prony Series Fit for Viscoelastic Data

This project provides a Python-based implementation to fit viscoelastic data using the **Prony series**. The code uses experimental data (storage modulus and loss modulus) and optimizes the Prony series parameters to model the material's behavior.

## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): E'(ω)
- **Loss modulus** (viscous behavior): E''(ω)

The equations for the Prony series used in this project are as follows:

### Storage Modulus E'(ω):

(https://render.githubusercontent.com/render/math?math=%5Clarge+E%27%28%5Comega%29+%3D+E_0+%5Cleft%281+-+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D+g_i+%2B+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D+g_i+%5Cfrac%7B%5Comega%5E2+%5Ctau_i%5E2%7D%7B1+%2B+%5Comega%5E2+%5Ctau_i%5E2%7D+%5Cright%29)

### Loss Modulus E''(ω):

(https://render.githubusercontent.com/render/math?math=%5Clarge+E%27%27%28%5Comega%29+%3D+E_0+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D+g_i+%5Cfrac%7B%5Comega+%5Ctau_i%7D%7B1+%2B+%5Comega%5E2+%5Ctau_i%5E2%7D)

Where:
- E₀ is the long-term modulus.
- gᵢ are the Prony series coefficients (describing the material's relaxation behavior).
- τᵢ are the relaxation times.
- ω is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://render.githubusercontent.com/render/math?math=%5Clarge+%5Ctext%7BMAPE%7D%28E%27%2C+E%27%27%29+%3D+%5Cfrac%7B100%7D%7Bn%7D+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D+%5Cleft%7C+%5Cfrac%7BE_%7B%5Ctext%7Bexp%7D%7D%27+-+E_%7B%5Ctext%7Bcalc%7D%7D%27%7D%7BE_%7B%5Ctext%7Bexp%7D%7D%27%7D+%5Cright%7C+%2B+%5Cfrac%7B100%7D%7Bn%7D+%5Csum_%7Bi%3D1%7D%5E%7Bn%7D+%5Cleft%7C+%5Cfrac%7BE_%7B%5Ctext%7Bexp%7D%7D%27%27+-+E_%7B%5Ctext%7Bcalc%7D%7D%27%27%7D%7BE_%7B%5Ctext%7Bexp%7D%7D%27%27%7D+%5Cright%7C)

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of NP candidate solutions x_i (i = 1, ..., NP) is randomly generated within the specified parameter bounds. Each x_i represents a set of Prony series parameters E_0, g_1, τ_1, ..., g_n, τ_n.

2. **Mutation**: 
   For each target vector x_i, a mutant vector v_i is created using the "best1bin" strategy:
   
   ![Mutation](https://render.githubusercontent.com/render/math?math=%5Clarge+v_i+%3D+x_%7Bbest%7D+%2B+F+%5Ccdot+%28x_%7Br1%7D+-+x_%7Br2%7D%29)
   
   Where x_best is the best solution so far, x_r1 and x_r2 are two randomly chosen distinct vectors, and F is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector u_i is created by mixing components of x_i and v_i:
   
   ![Crossover](https://render.githubusercontent.com/render/math?math=%5Clarge+u_%7Bi%2Cj%7D+%3D+%5Cbegin%7Bcases%7D+v_%7Bi%2Cj%7D+%26+%5Ctext%7Bif+%7D+%5Ctext%7Brand%7D%280%2C1%29+%5Cleq+CR+%5Ctext%7B+or+%7D+j+%3D+j_%7Brand%7D+%5C%5C+x_%7Bi%2Cj%7D+%26+%5Ctext%7Botherwise%7D+%5Cend%7Bcases%7D)
   
   Where CR is the crossover rate (set to 0.7 in this implementation) and j_rand is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector u_i is evaluated using the MAPE objective function. If it yields a lower MAPE than x_i, it replaces x_i in the next generation:
   
   ![Selection](https://render.githubusercontent.com/render/math?math=%5Clarge+x_i%5E%7BG%2B1%7D+%3D+%5Cbegin%7Bcases%7D+u_i%5EG+%26+%5Ctext%7Bif+%7D+f%28u_i%5EG%29+%3C+f%28x_i%5EG%29+%5C%5C+x_i%5EG+%26+%5Ctext%7Botherwise%7D+%5Cend%7Bcases%7D)
   
   Where f() represents the MAPE objective function and G is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of 1e-7.

[The rest of the content remains the same...]


This process is repeated for different numbers of Prony terms (from 1 to 10 by default) to find the optimal number of terms that best fits the experimental data. The algorithm's adaptive nature allows it to efficiently explore the parameter space and converge to a global optimum, making it well-suited for fitting the non-linear Prony series model to complex viscoelastic data.

The differential evolution algorithm is particularly effective for this problem because:
- It can handle non-linear, non-convex optimization landscapes.
- It doesn't require gradient information, which is beneficial for the complex Prony series equations.
- It's less likely to get trapped in local optima compared to traditional gradient-based methods.
- It can efficiently handle the varying number of parameters as the number of Prony terms changes.

By employing this sophisticated optimization technique, the code can accurately fit the Prony series to a wide range of viscoelastic materials, providing reliable parameters for use in finite element simulations and other material modeling applications.
