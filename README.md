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
