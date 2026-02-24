# #277 - Support for generating pep484 .pyi stubs [Open]

> Username: chadrik  
> Created at: 2019-11-04 00:28:22 UTC  
> Updated at: 2023-07-22 00:17:21 UTC  
> Url: https://github.com/boostorg/python/issues/277  

[Static typing in python](https://realpython.com/python-type-checking/) (aka PEP-484) is gaining a lot of traction, with linting tools like [mypy](https://mypy.readthedocs.io/en/latest/) reaching maturity, and support in IDEs -- like PyCharm, VSCode, and Sublime -- for using pyi stubs to drive code inspection and completion.  Stubs are particularly impactful for c/c++ bindings which are opaque to static inspectors.    
  
pyi stub generation is already supported by other python binding generators like pybind11, shiboken, and sip.  
  
Has this feature been discussed at all in boost-dev circles?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2019-11-04 00:39:47 UTC  
> Url: https://github.com/boostorg/python/issues/277#issuecomment-549196759  

I'm not aware of any such proposal, but I'd be open to consider any, notably if it has been tested with pybind11.

---

## Comment 2

> Username: qhaas  
> Created at: 2023-06-10 17:28:19 UTC  
> Url: https://github.com/boostorg/python/issues/277#issuecomment-1585745896  

I started using a library built with `boost::python` recently and discovered nearly immediately that vscode and pyright were not detecting the types.  I had to frequently consult the source code of the library to determine typing information, which hampered productivity.  
  
I forgot how reliant I have become on the features of PEP-484 and its standardized way of packaging typing information introduced in Python 3.7 with [PEP-561](https://peps.python.org/pep-0561/).  Including support for type hints would be a GREAT feature.

---

## Comment 3

> Username: CEXT-Dan  
> Created at: 2023-07-22 00:17:21 UTC  
> Url: https://github.com/boostorg/python/issues/277#issuecomment-1646352179  

Started wrapping a very large C++ project, found it’s too hard to use without proper stubs.  
So far I’ve been able to re-parse what boost::python emits to something semi usable, but it’s a combination of hand written and automation, very hard to maintain.    
I’m running embedded, couldn’t get routines like stubgen to work  
Any improved support would be grateful
