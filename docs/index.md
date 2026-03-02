# About Lyapy

`lyapy` is a pure Python library for the generation and analysis of synthetic chaotic time series derived from discrete dynamical systems with exact Lyapunov exponents and analytically known invariant measures.

## Installing

Lyapy can be installed via the command line using:

```
pip install lyapy
```

## Basic Usage

```python
from lyapy import LogisticMap

# Initialize the map with steps and transient
map_obj = LogisticMap(steps=1000, trans=100)

# Get a complete summary
summary = map_obj.lyapunov_summary()
print(f"Estimated Lambda: {summary['estimated']}")

# Generate plots
map_obj.time_series(plot=True)
map_obj.lyapunov_convergence(plot=True)
```

## Class Initialization

<div class="func-header">
    <b>ChaoticMap</b><span>(steps, trans, x0=None, prec=50, seed=None)</span>
</div>

Base class constructor for all chaotic maps. All map classes inherit from `ChaoticMap` and share this interface.

**Parameters:**

:   **steps** (int): Number of iterations used to compute the map evolution and Lyapunov average.

:   **trans** (int): Number of transient iterations to be discarded.

:   **x0** (float/Decimal, optional): Initial condition. If `None`, it is sampled randomly from the map's domain.

:   **prec** (int): Decimal precision for calculations (default: `50`).

:   **seed** (int, optional): Seed for the random number generator.

**Examples**

```python
>>> from lyapy import LogisticMap, TentMap
>>> m = LogisticMap(steps=1000, trans=100)
>>> m
<LogisticMap: x0=0.6931, steps=1000, trans=100>

>>> m = TentMap(steps=500, trans=50, x0=0.69, seed=42)
```

---

<div class="func-header">
    <b>available_maps</b><span>()</span>
</div>

Return the list of chaotic maps implemented in the package.

**Returns:**

:   **values** (*list of str*): Names of the available chaotic maps.

**Examples**

```python
>>> import lyapy
>>> lyapy.available_maps()
['Gauss',
 'Logistic',
 'Bernoulli',
 'Ulam',
 'Tent',
 'Asymmetric Tent',
 'Chebyshev',
 'Generalized Bernoulli']
```

---

## Methods

#### Time Series

<div class="func-header">
    <b>time_series</b><span>(dec=False, plot=False)</span>
</div>

Computes the evolution of the chaotic map after discarding the transient.

**Parameters:**

:   **dec** (*bool*): Whether to return a list of `Decimal` objects (if `True`) or a `numpy.array` (if `False`, default).

:   **plot** (*bool*): Whether to show a plot of the time series (default: `False`).

**Returns:**

:   **values** (*numpy.array or list of Decimal*): Time series of the chaotic map evolution.

**Examples**

```python
>>> m = TentMap(steps=1000, trans=100)
>>> m.time_series()
array([0.88, 0.24, 0.48, 0.96, ..., 0.64, 0.72, 0.56])

>>> m.time_series(dec=True)
[Decimal('0.88'), Decimal('0.24'), ...]

>>> m.time_series(plot=True)
# output plot
array([0.88, 0.24, 0.48, 0.96, ... , 0.64, 0.72, 0.56])
```

---

#### Lyapunov Estimated

<div class="func-header">
    <b>lyapunov_estimated</b><span>(dec=False)</span>
</div>

Computes the estimated Lyapunov Exponent of the chaotic map.

**Parameters:**

:   **dec** (*bool*): Whether to return a `Decimal` object (if `True`) or a `float` (if `False`, default).

**Returns:**

:   **values** (*float or Decimal*): The estimated Lyapunov Exponent.

**Examples**

```python
>>> m = TentMap(steps=1000, trans=100, x0=0.69)
>>> m.lyapunov_estimated()
0.6931471805599453

>>> m.lyapunov_estimated(dec=True)
Decimal('0.69314718055994530941723212145817656807550013436')
```

---

#### Lyapunov Convergence

<div class="func-header">
    <b>lyapunov_convergence</b><span>(plot=False)</span>
</div>

Computes the convergence of the Lyapunov Exponent over iterations.

**Parameters:**

:   **plot** (*bool*): Whether to show a plot of the convergence (default: `False`).

**Returns:**

:   **values** (*numpy.array*): Array of running Lyapunov Exponent averages at each iteration step.

**Examples**

```python
>>> m = LogisticMap(steps=1000, trans=1000)
>>> m.lyapunov_convergence()
array([0.90973773, 0.41082964, 0.69907782, ..., 0.69323049])

>>> m.lyapunov_convergence(plot=True)
# output plot
```

---

#### Theoretical Lyapunov

<div class="func-header">
    <b>theoretical_lyapunov</b>
</div>

Property that returns the analytical Lyapunov Exponent of the map.

**Returns:**

:   **values** (*Decimal or None*): The theoretical Lyapunov Exponent. Returns `None` for maps or parameter values where the theoretical value is undefined (e.g., `LogisticMap` with `r ≠ 4`).

**Examples**

```python
>>> m = TentMap(steps=1000, trans=100)
>>> float(m.theoretical_lyapunov)
0.6931471805599453

>>> m = LogisticMap(steps=1000, trans=100, r=3)
>>> m.theoretical_lyapunov is None
True
```

---

#### Lyapunov Summary

<div class="func-header">
    <b>lyapunov_summary</b><span>(dec=False)</span>
</div>

Generates a complete summary of the map, including the estimated and theoretical Lyapunov Exponents, relative error, and the full time series.

**Parameters:**

:   **dec** (*bool*): Whether to return high-precision `Decimal` values for all numeric fields (default: `False`).

**Returns:**

:   **values** (*dict*): Dictionary with the following keys:

| Key | Type | Description |
|-----|------|-------------|
| `map` | str | Class name of the map |
| `theoretical` | float or Decimal | Theoretical Lyapunov Exponent |
| `estimated` | float or Decimal | Estimated Lyapunov Exponent |
| `error_percent` | str | Relative error between estimated and theoretical (formatted string) |
| `steps` | int | Number of iterations used |
| `transient` | int | Number of transient iterations discarded |
| `x0` | float or Decimal | Initial condition used |
| `time_series` | numpy.array or list | Map orbit after transient |

**Examples**

```python
>>> m = TentMap(steps=1000, trans=100, x0=0.69)
>>> m.lyapunov_summary()
{'map': 'TentMap',
 'theoretical': 0.6931471805599453,
 'estimated': 0.6931471805599453,
 'error_percent': '0.0000%',
 'steps': 1000,
 'transient': 100,
 'x0': 0.69,
 'time_series': array([0.88, 0.24, 0.48, ...])}
```

---

## List of Maps

In chaos theory, maps (also known as iterated maps, difference equations, or recursion relations) are dynamical systems in which time is discrete rather than continuous.[^1] The simulation of discrete maps is fundamental, as they serve as controlled environments for the study of chaotic systems, allowing the observation of complex phenomena through the evolution of discrete orbits rather than continuous flows.[^1]

To see the available maps use `lyapy.available_maps()`.

---

### Logistic Map

**Class:** `LogisticMap(steps, trans, r=4, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = rx_n(1 - x_n)$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(2)$ *(only defined for $r = 4$)*

---

### Ulam Map

**Class:** `UlamMap(steps, trans, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = 1 - 2x_n^2$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(2)$

---

### Gauss Map

**Class:** `GaussMap(steps, trans, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = \frac{1}{x_n} \pmod 1$

**Theoretical Lyapunov Exponent:** $\lambda = \dfrac{\pi^2}{6\ln(2)}$

---

### Bernoulli Map

**Class:** `BernoulliMap(steps, trans, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = 2x_n \pmod 1$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(2)$

---

### Tent Map

**Class:** `TentMap(steps, trans, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = 2\min(x_n,\, 1 - x_n)$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(2)$

---

### Asymmetric Tent Map

**Class:** `AsymetricMap(steps, trans, x0=None, prec=50, seed=None)`

**Equation:**

$$x_{n+1} = \begin{cases} x_n / a & \text{if } x_n < a \\ (1 - x_n)/(1 - a) & \text{otherwise} \end{cases}$$

**Theoretical Lyapunov Exponent:** $\lambda = -a\ln(a) - (1-a)\ln(1-a)$

*(Default $a = 0.4$)*

---

### Chebyshev Map

**Class:** `ChebyshevMap(steps, trans, k=2, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = T_k(x_n)$, where $T_k$ is the Chebyshev polynomial of degree $k$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(k)$

*(Default $k = 2$)*

---

### Generalized Bernoulli Map

**Class:** `GeneralizedBernoulliMap(steps, trans, m=2, x0=None, prec=50, seed=None)`

**Equation:** $x_{n+1} = mx_n \pmod 1$

**Theoretical Lyapunov Exponent:** $\lambda = \ln(m)$

*(Default $m = 2$, which recovers the standard Bernoulli Map)*

---

## References

[^1]: STROGATZ, Steven H. **Nonlinear Dynamics and Chaos**: With Applications to Physics, Biology, Chemistry, and Engineering. 2. ed. CRC Press, 2014.
