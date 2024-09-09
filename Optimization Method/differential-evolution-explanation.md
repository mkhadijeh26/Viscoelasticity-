## Prony Series Overview

The **Prony series** is a mathematical model used to represent the viscoelastic behavior of materials. The material's modulus is decomposed into two parts:
- **Storage modulus** (elastic behavior): $E'(\\omega)$
- **Loss modulus** (viscous behavior): $E''(\\omega)$

The equations for the Prony series used in this project are as follows:

### Storage Modulus $E'(\\omega)$:

![Storage Modulus](https://latex.codecogs.com/svg.image?E'(\omega)%20=%20E_0%20\left(1%20-%20\sum_{i=1}^{n}%20g_i%20+%20\sum_{i=1}^{n}%20g_i%20\frac{\omega^2%20\tau_i^2}{1%20+%20\omega^2%20\tau_i^2}%20\right))

### Loss Modulus $E''(\\omega)$:

![Loss Modulus](https://latex.codecogs.com/svg.image?E''(\omega)%20=%20E_0%20\sum_{i=1}^{n}%20g_i%20\frac{\omega%20\tau_i}{1%20+%20\omega^2%20\tau_i^2})

Where:
- $E_0$ is the long-term modulus.
- $g_i$ are the Prony series coefficients (describing the material's relaxation behavior).
- $\\tau_i$ are the relaxation times.
- $\\omega$ is the angular frequency (rad/s).

## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://latex.codecogs.com/svg.image?\text{MAPE}(E',%20E'')%20=%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}'%20-%20E_{\text{calc}}'}{E_{\text{exp}}'}%20\right|%20+%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}''%20-%20E_{\text{calc}}''}{E_{\text{exp}}''}%20\right|)

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of NP candidate solutions $x_i$ ($i = 1, ..., NP$) is randomly generated within the specified parameter bounds. Each $x_i$ represents a set of Prony series parameters $E_0$, $g_1$, $\\tau_1$, ..., $g_n$, $\\tau_n$.

2. **Mutation**: 
   For each target vector $x_i$, a mutant vector $v_i$ is created using the "best1bin" strategy:
   
   ![Mutation](https://latex.codecogs.com/svg.image?v_i%20=%20x_{best}%20+%20F%20\cdot%20(x_{r1}%20-%20x_{r2}))
   
   Where $x_{best}$ is the best solution so far, $x_{r1}$ and $x_{r2}$ are two randomly chosen distinct vectors, and $F$ is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector $u_i$ is created by mixing components of $x_i$ and $v_i$:
   
   ![Crossover](https://latex.codecogs.com/svg.image?u_%7Bi%2Cj%7D%20%3D%20%5Cbegin%7Bcases%7D%20v_%7Bi%2Cj%7D%20%26%20%5Ctext%7Bif%20%7D%20%5Ctext%7Brand%7D(0%2C1)%20%5Cleq%20CR%20%5Ctext%7B%20or%20%7D%20j%20%3D%20j_%7Brand%7D%20%5C%5C%20x_%7Bi%2Cj%7D%20%26%20%5Ctext%7Botherwise%7D%20%5Cend%7Bcases%7D)
   
   Where $CR$ is the crossover rate (set to 0.7 in this implementation) and $j_{rand}$ is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector $u_i$ is evaluated using the MAPE objective function. If it yields a lower MAPE than $x_i$, it replaces $x_i$ in the next generation:
   
   ![Selection](https://latex.codecogs.com/svg.image?x_i%5E%7BG%2B1%7D%20%3D%20%5Cbegin%7Bcases%7D%20u_i%5EG%20%26%20%5Ctext%7Bif%20%7D%20f(u_i%5EG)%20%3C%20f(x_i%5EG)%20%5C%5C%20x_i%5EG%20%26%20%5Ctext%7Botherwise%7D%20%5Cend%7Bcases%7D)
   
   Where $f()$ represents the MAPE objective function and $G$ is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of $1e^{-7}$.

"""


In our Prony series fitting code, we use SciPy's implementation of differential evolution:

```python
from scipy.optimize import differential_evolution

result = differential_evolution(
    objective_function,
    bounds,
    args=(omega, storage_exp, loss_exp),
    strategy='best1bin',
    maxiter=1000,
    popsize=15,
    tol=1e-7,
    mutation=(0.5, 1),
    recombination=0.7
)
```
