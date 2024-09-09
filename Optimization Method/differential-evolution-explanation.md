## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): E'(ω)
- **Loss modulus** (viscous behavior): E''(ω)

The equations for the Prony series used in this project are as follows:

### Storage Modulus E'(ω):

E'(\omega) = E_0 \left(1 - \sum_{i=1}^{n} g_i + \sum_{i=1}^{n} g_i \frac{\omega^2 \tau_i^2}{1 + \omega^2 \tau_i^2} \right)

### Loss Modulus E''(ω):

```latex
E''(\omega) = E_0 \sum_{i=1}^{n} g_i \frac{\omega \tau_i}{1 + \omega^2 \tau_i^2}
```

Where:
- E₀ is the long-term modulus.
- gᵢ are the Prony series coefficients (describing the material's relaxation behavior).
- τᵢ are the relaxation times.
- ω is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

```latex
\text{MAPE}(E', E'') = \frac{100}{n} \sum_{i=1}^{n} \left| \frac{E_{\text{exp}}' - E_{\text{calc}}'}{E_{\text{exp}}'} \right| + \frac{100}{n} \sum_{i=1}^{n} \left| \frac{E_{\text{exp}}'' - E_{\text{calc}}''}{E_{\text{exp}}''} \right|
```

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of NP candidate solutions x_i (i = 1, ..., NP) is randomly generated within the specified parameter bounds. Each x_i represents a set of Prony series parameters E_0, g_1, τ_1, ..., g_n, τ_n.

2. **Mutation**: 
   For each target vector x_i, a mutant vector v_i is created using the "best1bin" strategy:
   
   ```latex
   v_i = x_{best} + F \cdot (x_{r1} - x_{r2})
   ```
   
   Where x_best is the best solution so far, x_r1 and x_r2 are two randomly chosen distinct vectors, and F is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector u_i is created by mixing components of x_i and v_i:
   
   ```latex
   u_{i,j} = \begin{cases} 
   v_{i,j} & \text{if } \text{rand}(0,1) \leq CR \text{ or } j = j_{rand} \\ 
   x_{i,j} & \text{otherwise} 
   \end{cases}
   ```
   
   Where CR is the crossover rate (set to 0.7 in this implementation) and j_rand is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector u_i is evaluated using the MAPE objective function. If it yields a lower MAPE than x_i, it replaces x_i in the next generation:
   
   ```latex
   x_i^{G+1} = \begin{cases} 
   u_i^G & \text{if } f(u_i^G) < f(x_i^G) \\ 
   x_i^G & \text{otherwise} 
   \end{cases}
   ```
   
   Where f() represents the MAPE objective function and G is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of 1e-7.


This process is repeated for different numbers of Prony terms (from 1 to 10 by default) to find the optimal number of terms that best fits the experimental data. The algorithm's adaptive nature allows it to efficiently explore the parameter space and converge to a global optimum, making it well-suited for fitting the non-linear Prony series model to complex viscoelastic data.

The differential evolution algorithm is particularly effective for this problem because:
- It can handle non-linear, non-convex optimization landscapes.
- It doesn't require gradient information, which is beneficial for the complex Prony series equations.
- It's less likely to get trapped in local optima compared to traditional gradient-based methods.
- It can efficiently handle the varying number of parameters as the number of Prony terms changes.

By employing this sophisticated optimization technique, the code can accurately fit the Prony series to a wide range of viscoelastic materials, providing reliable parameters for use in finite element simulations and other material modeling applications.
