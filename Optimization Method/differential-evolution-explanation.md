# Prony Series Optimization Algorithm

This document describes the optimization algorithm used to fit a Prony series to experimental data of storage and loss moduli.

## 1. Prony Series Model

The Prony series is used to model the viscoelastic behavior of materials. For dynamic modulus data, it can be expressed as:

### Storage Modulus:

$E'(\omega) = E_0 \left(1 - \sum_{i=1}^N g_i + \sum_{i=1}^N \frac{g_i \omega^2 \tau_i^2}{1 + \omega^2 \tau_i^2}\right)$

### Loss Modulus:

$E''(\omega) = E_0 \sum_{i=1}^N \frac{g_i \omega \tau_i}{1 + \omega^2 \tau_i^2}$

Where:
- $E_0$ is the long-term modulus
- $g_i$ are the relaxation strengths
- $\tau_i$ are the relaxation times
- $\omega$ is the angular frequency
- $N$ is the number of Prony series terms

## 2. Objective Function

The optimization aims to minimize the Mean Absolute Percentage Error (MAPE) between the experimental data and the Prony series model:

$MAPE = \frac{1}{2}\left(\frac{1}{n}\sum_{j=1}^n \left|\frac{E'_{exp,j} - E'_{calc,j}}{E'_{exp,j}}\right| + \frac{1}{n}\sum_{j=1}^n \left|\frac{E''_{exp,j} - E''_{calc,j}}{E''_{exp,j}}\right|\right) \times 100\%$

Where:
- $E'_{exp,j}$ and $E''_{exp,j}$ are the experimental storage and loss moduli
- $E'_{calc,j}$ and $E''_{calc,j}$ are the calculated storage and loss moduli from the Prony series
- $n$ is the number of data points

## 3. Optimization Algorithm: Differential Evolution

The code uses the Differential Evolution algorithm to optimize the Prony series parameters. This is a global optimization method that works well for non-convex problems.

### Algorithm Steps:

1. Initialize a population of candidate solutions (parameter sets) within specified bounds.
2. For each generation:
   a. Create new candidate solutions through mutation and crossover.
   b. Evaluate the fitness (MAPE) of each new solution.
   c. Select the better solutions for the next generation.
3. Repeat until convergence or maximum iterations reached.

### Key Parameters:

- Strategy: 'best1bin'
- Maximum iterations: 1000
- Population size: 15
- Tolerance: 1e-7
- Mutation constant: (0.5, 1)
- Recombination constant: 0.7

## 4. Adaptive Fitting

The code implements an adaptive fitting approach:

1. Start with 1 Prony term.
2. Optimize parameters using Differential Evolution.
3. Calculate MAPE for current fit.
4. Increase number of terms and repeat steps 2-3.
5. Continue up to a maximum number of terms (10 in this case).
6. Select the model with the lowest MAPE as the best fit.

This adaptive approach helps to determine the optimal number of Prony terms needed to accurately represent the material behavior without overfitting.

## 5. Parameter Bounds

The optimization is constrained by the following parameter bounds:

- $E_0$: $[0.5 \times \min(E'_{exp}), 2 \times \max(E'_{exp})]$
- $g_i$: $[0, 1]$
- $\tau_i$: $[1/\max(\omega), 1/\min(\omega)]$

These bounds ensure physically meaningful results and improve the efficiency of the optimization process.

By combining the Prony series model, MAPE objective function, Differential Evolution algorithm, and adaptive fitting approach, this method provides a robust way to characterize viscoelastic materials from dynamic modulus data.