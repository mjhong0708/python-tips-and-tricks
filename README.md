# python-tips-and-tricks

My collection of tips and tricks for python

> Disclaimer: some of these may be not appropriate for real application. Use it at own risk!

## 01. Use function with the name given by string

Suppose that you have a module `mymod`, like following:
```python
# mymod.py
def func1(x):
    ...
    
def func2(x):
    ...
```

Then if you get the name of function to use as `str` (by `input`, `config.json`, ...), use `getattr` on `mymod` to get the function.

```python
# main.py
import mymod

func_name = input("Enter the name of function:")
# or like config.get("func_name")...

func_to_use = getattr(mymod, func_name)
```

But this can lead to unwanted execution of unwanted function! To prevent this, simply declare a list of allowed functions.
```python
_allowed_funcs = ("func1", "func2")

if func_name not in _allowed_funcs:
    raise ValueError("It is not available!")
else:
    func_to_use = getattr(mymod, func_name)
```

## 02. Generate numpy compatible function with sympy

`sympy.lambdify` function allows us to convert `sympy` expression into python callable function. 

```python
import numpy as np
import sympy
from sympy.functions.special.polynomials import hermite

x = sympy.symbols("x")  # define symbol 
H_3 = hermite(3, x_sym)  # 3th Hermite polynomial
eval_hermite = sympy.lambdify(x, H_3, "numpy") # args: symbol, expression, backend

# Evaluate polynomial
x_vals = np.linspace(-1, 1, 100)
y_vals = eval_hermite(x_vals)
```

It also can be used with any framework that accepts numpy (ex. `jax`).

```python
from functools import partial

import jax
import jax.numpy as jnp
import numpy as np
import sympy
from sympy.functions.special.polynomials import hermite

# Can be jitted
@partial(jax.jit, static_argnums=(1,))
def hermite_fn(x, n):
    x_sym = sympy.symbols("x")
    H_n = hermite(n, x_sym)
    eval_hermite = sympy.lambdify(x_sym, H_n, "numpy")
    return eval_hermite(x)
    
x_vals = jnp.linspace(-3, 3.5, 10000)
y_vals = hermite_fn(xs, 2)
# jax.grad per x values
dy_dx_vals = jax.vmap(jax.grad(hermite_fn), (0, None))(xs, 2)
```
