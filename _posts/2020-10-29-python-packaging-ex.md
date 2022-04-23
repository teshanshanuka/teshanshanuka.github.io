---
layout: post
title:  "A minimal python packaging example"
date:   2020-10-29 01:01:01 +0530
categories: python packaging setuptools
---

*Packaging your python code as a python package lets you pip install it and use it from any place in your computer*

---

# Overview

Python is one of the best programming languages ever. However, when your python project gets bigger with multiple 
packages and files, some things can get tricky. One is importing from different packages from different locations.

I’ve had [this][1]{:target="_blank"} problem and could not find a good solution without doing appending unimportable 
packages’ paths to the scripts needed (with `sys.path.append('<dir>')`) which is ugly and not a good practice.

As a solution, you can have your core package pip installed alongside your python libraries so that they can be 
imported from anywhere. Here’s how...

# Package description

Here’s my directory structure. I have my core modules in my_pkg and a sub module sub_pkg inside

```sh
pkging_ex/
├── my_pkg
│   ├── foolib.py
│   ├── __init__.py
│   └── sub_pkg
│       ├── barlib.py
│       └── __init__.py
└── setup.py
```

Here’s the content of **mypkg**

```python
#mypkg/foolib.py

__all__ = ['foo', 'CFoo']

def foo():
    print('Foo function')

class CFoo:
    def __init__(self):
        print('CFoo class')
```

```python
#mypkg/__init__.py

from .foolib import *
```

The `__init__.py` has that import because I want to import the function `foo()` as `mypkg.foo` not `mypkg.foolib.foo` 
which would be the case if I didn’t have that import statement.

Here’s the content of **mypkg/sub_pkg**

```python
#mypkg/sub_pkg/barlib.py

__all__ = ['bar']

from ..foolib import foo, CFoo
def bar():
    print('Calling foo from bar')
    foo()
```

```python
#mypkg/sub_pkg/__init__.py

from .barlib import *
```

The `__all__` list at the top of modules makes sure that only the attributes specified in that list gets imported if 
you do an `import *` from the module.

# Installing
Now we need a **setup.py** specifying how to handle the packaging. Here’s mine for this package

```python
import setuptools

setuptools.setup(
    name="my_new_pkg",  # Some name for the package
    version="0.0.1",
    author="Teshan Liyanage",
    author_email="teshan@example.com",
    description="A small example package",
    packages=setuptools.find_packages(),
    python_requires='>=3.6'  # Say we need minimum python 3.6
 )
 ```

 Now just do `pip install .`

Here’s a simple sample test code to test if our package got installed correctly.

```python
from my_pkg import foo, CFoo
from my_pkg.sub_pkg import bar

c = CFoo()
bar()
```

You can run this and see that all the functions and classes are working fine if the package got installed correctly.

# Uninstalling

Just `pip uninstall my_new_pkg` where ‘my_new_pkg‘ is the name given to the package in the **setup.py**

Python packaging has many more capabilities and implications. Here I have only demonstrated a minimal example. Refer 
to [this][2] for a more detailed packaging guideline.

[1]: https://stackoverflow.com/questions/64427513/python3-package-file-cannot-import-from-same-directory-when-used-in-a-different
[2]: https://packaging.python.org/en/latest/
