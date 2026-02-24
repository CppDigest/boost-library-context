# #32 Python Port: Make importing more robust. [Merged]

> Username: frenchtoast747  
> Created at: 2014-09-03 18:52:39 UTC  
> Updated at: 2021-10-02 22:20:43 UTC  
> Merged at: 2014-09-10 17:55:21 UTC  
> Closed at: 2014-09-10 17:55:21 UTC  
> Url: https://github.com/boostorg/build/pull/32  

- create a b2.<whatever> index for O(1) package/module searching.  
- make the .pyc's sit next to their respective files; preventing  
  pollution of workspace  
- restore the `__file__` variable for imported files  
- prevent importing the same module twice under two separate names;  
  also preventing running initialization code twice (registering types  
  and features)  
- create an `__init__.py` for the contrib directory  
- requires Python 2.3 or newer for pkgutil  
  
The main issue I had was trying to import a module from Jamfiles as well as on the Python side, the module would end up being imported twice under two separate names. For example:  
  
```  
# in Jamroot.jam  
import msvc ;  
```  
  
This creates a python module instance named `msvc__for_jamfile` in `sys.modules` loading the `b2.tools.msvc` module and executing its initialization code.  
  
Now, say I have a Python module named `my_module` and in that module I have:  
  
```  
# my_module.py  
from b2.tools import msvc  
```  
  
This will create a module instance named `b2.tools.msvc` in `sys.modules` loading the `b2.tools.msvc` module and executing its initialization code again. This leads to re-registering types and features thus throwing errors.  
  
`msvc` was just an example of an existing module. I had the same problem with my own modules. My company has a fork of this repo and we put all of our company specific modules within the `contrib` folder to make it easier for each project to just clone a single repo rather than having them deal with python dependencies.  
  
This pull request addresses that issue as well as the `TODO` for cleaning up and using the `.pyc`'s. It also has the benefit of automatically finding and including any subpackage (like the contrib directory, hence the new `__init__.py`) within the `b2` namespace.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2014-09-10 16:24:46 UTC  
> Url: https://github.com/boostorg/build/pull/32#issuecomment-55141705  

Commit 60130a7 checks the `BOOST_BUILD_PATH` for modules outside of the `b2` package. Also, `imp.find_module()` is used in order to take advantage of the `pyc`s of those modules.

---
