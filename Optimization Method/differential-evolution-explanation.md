# Differential Evolution Optimization Method

## Overview

Differential Evolution (DE) is a population-based optimization algorithm used in this project to fit Prony series parameters to viscoelastic data. DE is particularly effective for global optimization problems with real-valued parameters, making it well-suited for our Prony series fitting task.

## Mathematical Formulation

Let $f(x)$ be the objective function to be minimized, where $x$ is a D-dimensional vector of real-valued parameters.

### 1. Initialization

Generate an initial population of NP parameter vectors:

$x_{i,G} = [x_{1,i,G}, x_{2,i,G}, ..., x_{D,i,G}]$

where $i = 1, 2, ..., NP$ and $G$ is the generation number.

### 2. Mutation

For each target vector $x_{i,G}$, generate a mutant vector $v_{i,G+1}$:

$v_{i,G+1} = x_{r1,G} + F \cdot (x_{r2,G} - x_{r3,G})$

where $r1, r2, r3 \in \{1, 2, ..., NP\}$ are random indices, mutually different and different from $i$. $F \in (0, 2]$ is the mutation factor.

### 3. Crossover

Generate a trial vector $u_{i,G+1}$ by binomial crossover:

$u_{j,i,G+1} = \begin{cases} 
v_{j,i,G+1} & \text{if } \text{rand}_j(0,1) \leq CR \text{ or } j = j_\text{rand} \\
x_{j,i,G} & \text{otherwise}
\end{cases}$

where $j = 1, 2, ..., D$, $\text{rand}_j(0,1)$ is a uniform random number, $CR \in [0,1]$ is the crossover rate, and $j_\text{rand} \in \{1, 2, ..., D\}$ is a random index.

### 4. Selection

Select the better vector between the target and trial vectors:

$x_{i,G+1} = \begin{cases}
u_{i,G+1} & \text{if } f(u_{i,G+1}) \leq f(x_{i,G}) \\
x_{i,G} & \text{otherwise}
\end{cases}$

### 5. Termination

Repeat steps 2-4 until a termination criterion is met (e.g., maximum number of generations or convergence threshold).

## Implementation in Our Code

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

The objective function in our case is the Mean Absolute Percentage Error (MAPE) between the experimental and calculated moduli:

$MAPE(E', E'') = \frac{100}{n} \sum_{i=1}^{n} \left| \frac{E'_{\text{exp}} - E'_{\text{calc}}}{E'_{\text{exp}}} \right| + \frac{100}{n} \sum_{i=1}^{n} \left| \frac{E''_{\text{exp}} - E''_{\text{calc}}}{E''_{\text{exp}}} \right|$

## Benefits of Differential Evolution for Prony Series Fitting

1. **Global Optimization**: DE is effective at finding the global minimum, reducing the risk of getting trapped in local minima, which is crucial for complex viscoelastic models.

2. **Parameter-Free**: DE requires minimal parameter tuning, making it robust across different datasets.

3. **Parallel Processing**: The population-based nature of DE allows for easy parallelization, potentially speeding up computation for large datasets.

4. **Handling Non-Differentiable Functions**: DE doesn't require the objective function to be differentiable, which is beneficial for complex material models.

5. **Constraint Handling**: DE can easily incorporate parameter constraints, which is important for ensuring physically meaningful Prony series parameters.

6. **Adaptability**: DE can adapt its search strategy during the optimization process, making it efficient for a wide range of problem landscapes.

7. **Robustness to Noise**: DE is relatively robust to noise in the objective function, which can be present in experimental viscoelastic data.

## Conclusion

The differential evolution algorithm provides a robust and efficient method for optimizing Prony series parameters. Its ability to handle complex, non-linear optimization problems makes it particularly well-suited for fitting viscoelastic models to experimental data. The implementation in our code leverages these strengths to ensure accurate and reliable fits for a wide range of viscoelastic materials.
