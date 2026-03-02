# Sobre o Lyapy

`lyapy` é uma biblioteca Python pura para geração e análise de séries temporais caóticas sintéticas derivadas de sistemas dinâmicos discretos, com expoentes de Lyapunov exatos e medidas invariantes analiticamente conhecidas.

## Instalação

O Lyapy pode ser instalado via linha de comando usando:

```
pip install lyapy
```

## Uso Básico

```python
from lyapy import LogisticMap

# Inicializa o mapa com número de iterações e transiente
map_obj = LogisticMap(steps=1000, trans=100)

# Obtém um resumo completo
summary = map_obj.lyapunov_summary()
print(f"Lambda Estimado: {summary['estimated']}")

# Gera gráficos
map_obj.time_series(plot=True)
map_obj.lyapunov_convergence(plot=True)
```

## Inicialização da Classe

<div class="func-header">
    <b>ChaoticMap</b><span>(steps, trans, x0=None, prec=50, seed=None)</span>
</div>

Classe base para todos os mapas caóticos. Todas as classes de mapa herdam de `ChaoticMap` e compartilham esta interface.

**Parâmetros:**

:   **steps** (int): Número de iterações usadas para calcular a evolução do mapa e a média de Lyapunov.

:   **trans** (int): Número de iterações transientes a serem descartadas.

:   **x0** (float/Decimal, opcional): Condição inicial. Se `None`, é amostrada aleatoriamente do domínio do mapa.

:   **prec** (int): Precisão decimal para os cálculos (padrão: `50`).

:   **seed** (int, opcional): Semente para o gerador de números aleatórios.

**Exemplos**

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

Retorna a lista de mapas caóticos implementados no pacote.

**Retorno:**

:   **values** (*list of str*): Nomes dos mapas caóticos disponíveis.

**Exemplos**

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

## Métodos

#### Série Temporal

<div class="func-header">
    <b>time_series</b><span>(dec=False, plot=False)</span>
</div>

Calcula a evolução do mapa caótico após descartar o transiente.

**Parâmetros:**

:   **dec** (*bool*): Se `True`, retorna uma lista de objetos `Decimal`; se `False` (padrão), retorna um `numpy.array`.

:   **plot** (*bool*): Se `True`, exibe um gráfico da série temporal (padrão: `False`).

**Retorno:**

:   **values** (*numpy.array ou list of Decimal*): Série temporal da evolução do mapa caótico.

**Exemplos**

```python
>>> m = TentMap(steps=1000, trans=100)
>>> m.time_series()
array([0.88, 0.24, 0.48, 0.96, ..., 0.64, 0.72, 0.56])

>>> m.time_series(dec=True)
[Decimal('0.88'), Decimal('0.24'), ...]

>>> m.time_series(plot=True)
# exibe gráfico
array([0.88, 0.24, 0.48, 0.96, ... , 0.64, 0.72, 0.56])
```

---

#### Expoente de Lyapunov Estimado

<div class="func-header">
    <b>lyapunov_estimated</b><span>(dec=False)</span>
</div>

Calcula o Expoente de Lyapunov estimado do mapa caótico.

**Parâmetros:**

:   **dec** (*bool*): Se `True`, retorna um objeto `Decimal`; se `False` (padrão), retorna um `float`.

**Retorno:**

:   **values** (*float ou Decimal*): O Expoente de Lyapunov estimado.

**Exemplos**

```python
>>> m = TentMap(steps=1000, trans=100, x0=0.69)
>>> m.lyapunov_estimated()
0.6931471805599453

>>> m.lyapunov_estimated(dec=True)
Decimal('0.69314718055994530941723212145817656807550013436')
```

---

#### Convergência de Lyapunov

<div class="func-header">
    <b>lyapunov_convergence</b><span>(plot=False)</span>
</div>

Calcula a convergência do Expoente de Lyapunov ao longo das iterações.

**Parâmetros:**

:   **plot** (*bool*): Se `True`, exibe um gráfico da convergência (padrão: `False`).

**Retorno:**

:   **values** (*numpy.array*): Array com as médias acumuladas do Expoente de Lyapunov a cada iteração.

**Exemplos**

```python
>>> m = LogisticMap(steps=1000, trans=1000)
>>> m.lyapunov_convergence()
array([0.90973773, 0.41082964, 0.69907782, ..., 0.69323049])

>>> m.lyapunov_convergence(plot=True)
# exibe gráfico
```

---

#### Expoente de Lyapunov Teórico

<div class="func-header">
    <b>theoretical_lyapunov</b>
</div>

Propriedade que retorna o Expoente de Lyapunov analítico do mapa.

**Retorno:**

:   **values** (*Decimal ou None*): O Expoente de Lyapunov teórico. Retorna `None` para mapas ou valores de parâmetros onde o valor teórico não está definido (ex.: `LogisticMap` com `r ≠ 4`).

**Exemplos**

```python
>>> m = TentMap(steps=1000, trans=100)
>>> float(m.theoretical_lyapunov)
0.6931471805599453

>>> m = LogisticMap(steps=1000, trans=100, r=3)
>>> m.theoretical_lyapunov is None
True
```

---

#### Resumo de Lyapunov

<div class="func-header">
    <b>lyapunov_summary</b><span>(dec=False)</span>
</div>

Gera um resumo completo do mapa, incluindo os Expoentes de Lyapunov estimado e teórico, erro relativo e a série temporal completa.

**Parâmetros:**

:   **dec** (*bool*): Se `True`, retorna valores `Decimal` de alta precisão em todos os campos numéricos (padrão: `False`).

**Retorno:**

:   **values** (*dict*): Dicionário com as seguintes chaves:

| Chave | Tipo | Descrição |
|-------|------|-----------|
| `map` | str | Nome da classe do mapa |
| `theoretical` | float ou Decimal | Expoente de Lyapunov teórico |
| `estimated` | float ou Decimal | Expoente de Lyapunov estimado |
| `error_percent` | str | Erro relativo entre estimado e teórico (string formatada) |
| `steps` | int | Número de iterações utilizadas |
| `transient` | int | Número de iterações transientes descartadas |
| `x0` | float ou Decimal | Condição inicial utilizada |
| `time_series` | numpy.array ou list | Órbita do mapa após o transiente |

**Exemplos**

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

## Lista de Mapas

Na teoria do caos, mapas (também conhecidos como mapas iterados, equações de diferenças ou relações de recorrência) são sistemas dinâmicos nos quais o tempo é discreto em vez de contínuo.[^1] A simulação de mapas discretos é fundamental, pois eles servem como ambientes controlados para o estudo de sistemas caóticos, permitindo a observação de fenômenos complexos por meio da evolução de órbitas discretas em vez de fluxos contínuos.[^1]

Para ver os mapas disponíveis, utilize `lyapy.available_maps()`.

---

### Mapa Logístico

**Classe:** `LogisticMap(steps, trans, r=4, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = rx_n(1 - x_n)$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(2)$ *(definido apenas para $r = 4$)*

---

### Mapa de Ulam

**Classe:** `UlamMap(steps, trans, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = 1 - 2x_n^2$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(2)$

---

### Mapa de Gauss

**Classe:** `GaussMap(steps, trans, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = \frac{1}{x_n} \pmod 1$

**Expoente de Lyapunov Teórico:** $\lambda = \dfrac{\pi^2}{6\ln(2)}$

---

### Mapa de Bernoulli

**Classe:** `BernoulliMap(steps, trans, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = 2x_n \pmod 1$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(2)$

---

### Mapa Tenda

**Classe:** `TentMap(steps, trans, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = 2\min(x_n,\, 1 - x_n)$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(2)$

---

### Mapa Tenda Assimétrico

**Classe:** `AsymetricMap(steps, trans, x0=None, prec=50, seed=None)`

**Equação:**

$$x_{n+1} = \begin{cases} x_n / a & \text{se } x_n < a \\ (1 - x_n)/(1 - a) & \text{caso contrário} \end{cases}$$

**Expoente de Lyapunov Teórico:** $\lambda = -a\ln(a) - (1-a)\ln(1-a)$

*(Padrão $a = 0.4$)*

---

### Mapa de Chebyshev

**Classe:** `ChebyshevMap(steps, trans, k=2, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = T_k(x_n)$, onde $T_k$ é o polinômio de Chebyshev de grau $k$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(k)$

*(Padrão $k = 2$)*

---

### Mapa de Bernoulli Generalizado

**Classe:** `GeneralizedBernoulliMap(steps, trans, m=2, x0=None, prec=50, seed=None)`

**Equação:** $x_{n+1} = mx_n \pmod 1$

**Expoente de Lyapunov Teórico:** $\lambda = \ln(m)$

*(Padrão $m = 2$, que recupera o Mapa de Bernoulli padrão)*

---

## Referências

[^1]: STROGATZ, Steven H. **Dinâmica Não-Linear e Caos**: Com Aplicações em Física, Biologia, Química e Engenharia. 2. ed. CRC Press, 2014.
