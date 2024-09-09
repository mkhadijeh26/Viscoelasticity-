## Algorithm

The core of the implementation is based on **differential evolution (DE)**, a powerful global optimization technique. The optimization process aims to minimize the error between the experimental and predicted moduli, based on the following **Mean Absolute Percentage Error (MAPE)** objective function:

![MAPE](https://latex.codecogs.com/svg.latex?\text{MAPE}(E',%20E'')%20=%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}'%20-%20E_{\text{calc}}'}{E_{\text{exp}}'}%20\right|%20+%20\frac{100}{n}%20\sum_{i=1}^{n}%20\left|%20\frac{E_{\text{exp}}''%20-%20E_{\text{calc}}''}{E_{\text{exp}}''}%20\right|)

The **differential evolution** algorithm works as follows:

1. **Initialization**: 
   A population of ![NP](https://latex.codecogs.com/svg.latex?NP) candidate solutions ![x_i](https://latex.codecogs.com/svg.latex?x_i) (i = 1, ..., NP) is randomly generated within the specified parameter bounds. Each ![x_i](https://latex.codecogs.com/svg.latex?x_i) represents a set of Prony series parameters ![E_0, g_1, \tau_1, ..., g_n, \tau_n](https://latex.codecogs.com/svg.latex?E_0,%20g_1,%20\tau_1,%20...,%20g_n,%20\tau_n).

2. **Mutation**: 
   For each target vector ![x_i](https://latex.codecogs.com/svg.latex?x_i), a mutant vector ![v_i](https://latex.codecogs.com/svg.latex?v_i) is created using the "best1bin" strategy:
   
   ![v_i = x_{best} + F \cdot (x_{r1} - x_{r2})](https://latex.codecogs.com/svg.latex?v_i%20=%20x_{best}%20+%20F%20\cdot%20(x_{r1}%20-%20x_{r2}))
   
   Where ![x_{best}](https://latex.codecogs.com/svg.latex?x_{best}) is the best solution so far, ![x_{r1}](https://latex.codecogs.com/svg.latex?x_{r1}) and ![x_{r2}](https://latex.codecogs.com/svg.latex?x_{r2}) are two randomly chosen distinct vectors, and ![F](https://latex.codecogs.com/svg.latex?F) is the mutation factor (set to a range of [0.5, 1] in this implementation).

3. **Crossover**: 
   A trial vector ![u_i](https://latex.codecogs.com/svg.latex?u_i) is created by mixing components of ![x_i](https://latex.codecogs.com/svg.latex?x_i) and ![v_i](https://latex.codecogs.com/svg.latex?v_i):
   
   ![u_{i,j} = \begin{cases} v_{i,j} & \text{if } \text{rand}(0,1) \leq CR \text{ or } j = j_{rand} \\ x_{i,j} & \text{otherwise} \end{cases}](https://latex.codecogs.com/svg.latex?u_{i,j}%20=%20\begin{cases}%20v_{i,j}%20&%20\text{if%20}%20\text{rand}(0,1)%20\leq%20CR%20\text{%20or%20}%20j%20=%20j_{rand}%20\\%20x_{i,j}%20&%20\text{otherwise}%20\end{cases})
   
   Where ![CR](https://latex.codecogs.com/svg.latex?CR) is the crossover rate (set to 0.7 in this implementation) and ![j_{rand}](https://latex.codecogs.com/svg.latex?j_{rand}) is a randomly chosen index to ensure that at least one component is always inherited from the mutant vector.

4. **Selection**: 
   The trial vector ![u_i](https://latex.codecogs.com/svg.latex?u_i) is evaluated using the MAPE objective function. If it yields a lower MAPE than ![x_i](https://latex.codecogs.com/svg.latex?x_i), it replaces ![x_i](https://latex.codecogs.com/svg.latex?x_i) in the next generation:
   
   ![x_i^{G+1} = \begin{cases} u_i^G & \text{if } f(u_i^G) < f(x_i^G) \\ x_i^G & \text{otherwise} \end{cases}](https://latex.codecogs.com/svg.latex?x_i^{G+1}%20=%20\begin{cases}%20u_i^G%20&%20\text{if%20}%20f(u_i^G)%20<%20f(x_i^G)%20\\%20x_i^G%20&%20\text{otherwise}%20\end{cases})
   
   Where ![f()](https://latex.codecogs.com/svg.latex?f()) represents the MAPE objective function and ![G](https://latex.codecogs.com/svg.latex?G) is the current generation.

5. **Stopping Criteria**: 
   The algorithm continues for a maximum of 1000 iterations or until the population converges to within a tolerance of 1e-7.

This process is repeated for different numbers of Prony terms (from 1 to 10 by default) to find the optimal number of terms that best fits the experimental data. The algorithm's adaptive nature allows it to efficiently explore the parameter space and converge to a global optimum, making it well-suited for fitting the non-linear Prony series model to complex viscoelastic data.

The differential evolution algorithm is particularly effective for this problem because:
- It can handle non-linear, non-convex optimization landscapes.
- It doesn't require gradient information, which is beneficial for the complex Prony series equations.
- It's less likely to get trapped in local optima compared to traditional gradient-based methods.
- It can efficiently handle the varying number of parameters as the number of Prony terms changes.

By employing this sophisticated optimization technique, the code can accurately fit the Prony series to a wide range of viscoelastic materials, providing reliable parameters for use in finite element simulations and other material modeling applications.
