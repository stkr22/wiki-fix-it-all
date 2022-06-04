# Python Basics

## What is the purpose of __init__.py?

This files indicates that the current folder is a submodule or module of a package or application. It has two main functions:

1. Allow python parsers like pylint to understand that they are in a subfolder and need to move up to get to the rootfolder of a package (without __init__.py)
2. It allows one to import functions of a module without referencing every single file. Instead one can simply import the module and this will automatically import all methods/functions/classes that are referenced in the __init__.py file. Example:

> Mypackage
> main.py
> -mymodule
> --__init__.py
> --myfunction.py

We could now either import like

``` py
import mymodule as module
```

in this case every function import inside the __init__.py would be imported.

PLEASE NOTE: The searchpath always starts at wherever the input file was localted. Thus your main.py should always be in the top directory!!!!

"The directory containing the input script (or the current directory when no file is specified)."

|_myproj
  -main.py
  -module1
  --something.py
  -module2

Sources:

- <https://docs.python.org/3/tutorial/modules.html#the-module-search-path>

## How do I use python f strings?

Sources:

- <https://realpython.com/python-f-strings/>

## Tests

### How do I write tests?

Use pytest for that. Fixtures can be used to definde stuff that is available to all tests. These are stored in the "conftest.py" file. Please note that all files and function names should be included in the test (except for conftest.py) should use the prefix "test_".

### How do I generate test data?

Use factory_boy for classes and faker standalone for primitive types.

Sources:

- <https://faker.readthedocs.io/en/stable/pytest-fixtures.html>
- <https://github.com/FactoryBoy/factory_boy>