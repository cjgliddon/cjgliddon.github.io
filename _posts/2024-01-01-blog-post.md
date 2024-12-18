---
title: 'Sketches of Python Aquaplanets'
date: 2024-01-01
permalink: /posts/2025/01/gcm-notes/
tags:
  - research
  - gcms
  - coding
---

# Important Python Preliminaries

## Modules, `__name__`, and `__main__`

A *module* is just a Python file containing a collection of functions. You can define your own modules and import them in the same way that you import your standard Python libraries like NumPy. If you have a module named, e.g., `functions.py`, you can import it as using the standard ``import functions`` syntax, and then call functions from the module using the syntax ``functions.f1()``, ``functions.f2()``, etc. 

Every module has an attribute `__name__` that returns the the module's name as a string. So, for instance, calling `functions.__name__` after importing it will return the string `'functions'`. If you're importing a file from a package, calling that file's `__name__` attribute will print the path from the parent file, e.g.:  
```python
>>> from scipy.integrate import solve_ivp
>>> solve_ivp.__name__
'scipy.integrate.solve_ivp'
```

The name of a module will, however, be reset to the special string `'__main__'` if it is executed in the top-level code environment. You can do this if you give a command to the Python interpreter with the following structure:
```python
$ python functions.py <arguments>
```
where the `<arguments>` are optional. By including the conditional `if __name__ == '__main__':` at the end of a module file, you can prescribe certain actions to take place if the module is run, potentially involving the arguments. Consider, for example, the following code:
```python
if __name__ == "__main__":
    import sys
    f1(int(sys.argv[1]))
```
which will, if the module is executed in the top-level code environment, execute the function f1 using the first additional argument given to the interpreter, which has been typed as an integer. The variable `sys.argv` is a list of command-line inputs passed to a Python script; `sys.argv[0]` is always the name of the script (`functions.py` in our example). 

Pretty much all of the modules in the Python wrapper of the GCM will end with this syntax, so keep a weather eye out.

## `pickle`

A module that can convert Python objects into binary files and vice versa. The key 

# Functions, Functions, Functions

## dns_frierson.py

This module executes a "direct numerical simulation." The parameters you can include in a call of the script to the interpreter include a procedure (by default, `"meta"`; optionally, `"single"`) and a list of integers, by default 1 to 20, which are a numeral index for each `experiment` (the variable name is `i_expt`, i.e., "experiment index number").  

### `dns_paramset(i_expt)`

Generates parameters for the direct numerical simulation.

These parameters include:

* `config_gcm`: this is the default configuration of the Frierson aquaplanet GCM, expressed as a dictionary of parameters. See the `frierson_gcm.py` file for details.

* `config_algo`: configuration of the DNS algorithm, also expressed as a dictionary of parameters. Some details that I don't understand here (what is "seed"?) but the important thing here is that the algorithm works in "chunks," with duration set by `'max_member_duration_phys'` and the number of chunks limited to `'num_chunks_max'`. This dictionary will later be used as an imput to a method in the file `algorithms_frierson.py`, so perhaps check there for details?

### `dns_single_workflow(i_expt)`

This function first generates the parameters from `dns_paramset(i_expt)`, then even more parameters (labels?) from the Frierson GCM methods. 

### `dns_single_procedure(i_expt)`

Executes a single run of the direct numerical simulation plus associated steps: plotting figures and computing some basic statistics of the model run. Each task has its own associated function; the actual numerical run happens in the function `run_dns`. The variable `i_expt` is the experiment number

### `run_dns(dirdict,filedict,config_gcm,config_algo)`

Executes a run of the direct numerical simulation. The parameters `dirdict`, `filedict`, `config_gcm`, and `config_algo` are generated from the method `dns_single_workflow`.
