# About Lyappy

`lyappy` is a pure Python library for the generation and analysis of synthetic chaotic time series derived from discrete dynamical systems with exact Lyapunov exponents and analytically known invariant measures.

## Installing

Lyappy can be installed via the command line using:

* `pip install lyappy`

## Basic Usage

```python
from lyappy import LogisticMap

# Initialize the map with steps and transient
map_obj = LogisticMap(steps=1000, trans=100)

# Get a complete summary
summary = map_obj.lyapunov_summary()
print(f"Estimated Lambda: {summary['estimated']}")

# Generate plots
map_obj.time_series(plot=True)
map_obj.lyapunov_convergence(plot=True)

```

## List of Methods

#### ChaoticMap Initialization

<div class="func-header">
    <b>ChaoticMap</b><span>(steps, trans, x0=None, prec=50, seed=None)</span>
</div>

Base class constructor for all chaotic maps.

**Parameters:**

:   **steps** (int): Number of iterations used to compute the map evolution and Lyapunov average.

:   **trans** (int): Number of transient iterations to be discarded.

:   **x0** (float/Decimal, optional): Initial condition. If None, it is sampled from the map's domain.

:   **prec** (int): Decimal precision for calculations (default: 50).

:   **seed** (int, optional): Seed for the random number generator.

<div class="func-header">
    <b>available_maps</b><span>()</span>
</div>

Return the list of chaotic maps implemented in the package.

**Returns:**

:   **values** (*list of str*): Names of the available chaotic maps.

**Return type:**

:   list

**Examples**
```python
>>> available_maps()
['Gauss',
 'Logistic',
 'Bernoulli',
 'Ulam',
 'Tent',
 'Asymmetric Tent',
 'Chebyshev']
>>>
```

#### Inicial Condition Generator

<div class="func-header">
    <b>initial_condition</b><span>(map_name)</span>
</div>

Return a random initial condition within the domain of the selected chaotic map.

**Parameters:**

:   **map_name** (*str*): Name of the chaotic map.

**Returns:**

:   **value** (*float*): A random initial condition sampled from the map domain.

**Return type:**

:   float

**Examples**
```python
>>> initial_condition("Tent")
0.3416435874184909
>>>
``` 

#### Map Evolution

<div class="func-header">
    <b>map_time_series</b><span>(map_name, x0, steps, trans, prec=50, dec=False, plot=False)</span>
</div>

Computes the evolution of a chaotic map.

**Parameters:**

:   **map_name** (*str*): Name of the chaotic map.
:   **x0** (*float*): Initial condition for the iterations.
:   **steps** (*int*): Number of iterations used to compute the map evolution.
:   **trans** (*int*): Number of transient iterations to be discarded.
:   **prec** (*int*): Decimal precision (default: 50).
:   **dec** (*bool*): Whether to return a `Decimal` object (if `True`) or a `np.array` (if `False`, default).
:   **plot** (*bool*): Whether to show a plot of the convergence evolution (default: `False`).

**Returns:**
:   **values** (*array or Decimal*): Time series containing the evolution of the chaotic map.

**Return type:**
:   numpy.array or decimal.Decimal

**Examples**


``` python
>>> map_time_serie("Logistic", 0.69, 1000, 100)
array([9.66107169e-01, 1.30976429e-01, 4.55286417e-01,...,2.03055676e-01])
>>>
>>> map_time_serie("Ulam", 0.69, 1000, 100, dec=True)
[Decimal('-0.6870338025456384356961511819355663023943768799766'),Decimal('0.05596910831936139542992034181417084026349458638572'),...,Decimal('0.11798100275668302052603852642095837137197711931708')]
>>>
>>> map_time_serie("Tent", 0.69, 1000, 100, plot=True)
output plot
array([0.88, 0.24, 0.48, 0.96, ... , 0.64, 0.72, 0.56])
``` 

!["Example of output plot"](download (3).png)]

#### Lyapunov Estimated

<div class="func-header">
    <b>lyapunov_estimated</b><span>(map_name, x0, steps, trans, prec=50, dec=False)</span>
</div>

Computes the estimated Lyapunov Exponent of a chaotic map.

**Parameters:**
:   **map_name** (*str*): Name of the chaotic map.
:   **x0** (*float*): Initial condition.
:   **steps** (*int*): Number of iterations used in the Lyapunov average.
:   **trans** (*int*): Number of transient iterations discarded.
:   **prec** (*int*): Decimal precision.
:   **dec** (*bool*): Whether to return a `Decimal` object (if `True`) or a `float` (if `False`, default).

**Returns:**
:   **values** (*float or Decimal*): The estimated Lyapunov Exponent of the chaotic map.

**Return type:**
:   float or decimal.Decimal

**Examples**

```python
>>>lyapunov_estimated("Tent", 0.69, 1000, 100)
0.6931471805599453
>>>
>>>lyapunov_estimated("Tent", 0.69, 1000, 100, dec=True)
Decimal('0.69314718055994530941723212145817656807550013436')

```

#### Lyapunov Convergence

<div class="func-header">
    <b>lyapunov_convergence</b><span>(map_name, x0, steps, trans, prec=50, dec=False, plot=False)</span>
</div>

Computes the convergence of the Lyapunov Exponent of a chaotic map.

**Parameters:**
:   **map_name** (*str*): Name of the chaotic map.
:   **x0** (*float*): Initial condition.
:   **steps** (*int*): Number of iterations used in the Lyapunov average.
:   **trans** (*int*): Number of transient iterations discarded.
:   **prec** (*int*): Decimal precision.
:   **dec** (*bool*): Whether to return a `Decimal` object.
:   **plot** (*bool*): Whether to show a plot of the convergence.

**Returns:**
:   **values** (*array or Decimal*): The convergence values of the Lyapunov Exponent.

**Return type:**
:   numpy.array or decimal.Decimal

```python
>>>x0 = initial_condition("Logistic")
>>>lyapunov_convergence("Logistic", x0, 1000, 1000, plot=True)
array([0.90973773, 0.41082964, 0.69907782, 0.74430939..., 0.69323049])
```
!["Example of output plot"](download (4).png)]

#### Theoretical Lyapunov

<div class="func-header">
    <b>theoretical_lyapunov</b><span>(map_name, dec=False)</span>
</div>

Computes the theoretical Lyapunov Exponent of a chaotic map.

**Parameters:**
:   **map_name** (*str*): Name of the chaotic map.
:   **dec** (*bool*): Whether to return a `Decimal` object (if `True`) or a `float` (if `False`, default).

**Returns:**
:   **values** (*float or Decimal*): The theoretical Lyapunov Exponent of the chaotic map.

**Return type:**
:   float or decimal.Decimal

```python
>>>theoretical_lyapunov("Tent")
0.6931471805599453
``` 

#### Lyapunov Summary

<div class="func-header">
    <b>lyapunov_summary</b><span>(map_name, x0, steps, trans, prec=50, dec=False, plot=False)</span>
</div>

**Parameters:**
:   **map_name** (*str*): Name of the chaotic map.
:   **x0** (*float*): Initial condition.
:   **steps** (*int*): Number of iterations used in the Lyapunov average.
:   **trans** (*int*): Number of transient iterations discarded.
:   **prec** (*int*): Decimal precision.
:   **dec** (*bool*): Whether to return a `Decimal` object.
:   **plot** (*bool*): Whether to show a summary plot.

**Returns:**
:   **values** (*dict*): Dictionary containing the chaotic map's name, theoretical Lyapunov Exponent, estimated Lyapunov Exponent, and the map's time series.

**Return type:**
:   dict

```python
>>>lyapunov_summary("Tent", 0.69, 1000, 100)
{'map': 'Tent map',
 'theoretical': 0.6931471805599453,
 'estimated': 0.6931471805599453,
 'time_serie': array([0.88, 0.24, 0.48, 0.96, 0.08, 0.16, 0.32,..., 0.72, 0.56])}
```
## List of Maps

In chaos theory, maps (also known as iterated maps, difference equations, or recursion relations) are dynamical systems in which time is discrete rather than continuous. [^1] The simulation of discrete maps is fundamental, as they serve as controlled environments for the study of chaotic systems, allowing the observation of complex phenomena through the evolution of discrete orbits rather than continuous flows.[^1]

To see the avaliable maps you can use the `available_maps()` method

The following maps can be simulated with this package:

### Logistic Map

Class: `LogisticMap`

Equation: $x_{n+1} = 4x_n(1 - x_n)$

Theoretical Lyapunov Exponent: $\lambda = \ln(2)$

### Ulam Map

Class: `UlamMap`

Equation: $x_{n+1} = 1 - 2x_n^2$

Theoretical Lyapunov Exponent: $\lambda = \ln(2)$

### Gauss Map

Class: `GaussMap`

Equation: $x_{n+1} = \frac{1}{x_n} \pmod 1$

Theoretical Lyapunov Exponent: $\lambda = \frac{\pi^2}{6 \ln(2)}$

### Bernoulli Map

Class: `BernoulliMap`

Equation: $x_{n+1} = 2x_n \pmod 1$

Theoretical Lyapunov Exponent: $\lambda = \ln(2)$

### Tent Map

Class: `TentMap`

Equation: $x_{n+1} = 2 \min(x_n, 1 - x_n)$

Theoretical Lyapunov Exponent: $\lambda = \ln(2)$

### Asymmetric Tent Map

Class: AsymetricMap

Equation: $x_{n+1} = x/a$ if $x < a$ else $(1-x)/(1-a)$

### Chebyshev Map

Class: ChebyshevMap

Equation: $x_{n+1} = 2x_n^2 - 1$

Theoretical Lyapunov Exponent: $\lambda = \ln(2)$

## References

[^1]: [^1]: STROGATZ, Steven H. **Nonlinear Dynamics and Chaos**: With Applications to Physics, Biology, Chemistry, and Engineering. 2. ed. [S.l.]: CRC Press, 2014.
