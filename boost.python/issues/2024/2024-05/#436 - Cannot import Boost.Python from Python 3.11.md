# #436 - Cannot import Boost.Python from Python 3.11 [Open]

> Username: debemdeboas  
> Created at: 2024-05-17 16:54:08 UTC  
> Updated at: 2024-05-17 19:09:12 UTC  
> Url: https://github.com/boostorg/python/issues/436  

Hi all,  
I'm trying to `import Boost.Python` from Python 3.11 and I'm getting a `ModuleNotFoundError`:  
I'm using Boost 1.82.0 on Ubuntu x86_64.  
  
```  
>>> import Boost.Python  
Traceback (most recent call last):  
  File "<stdin>", line 1, in <module>  
ModuleNotFoundError: No module named 'Boost'  
```  
  
I've built Boost.Python using the following bootstrap/`b2` commands:  
  
```  
$ ./bootstrap.sh --with-python-version=3.11 --with-libraries=python  
$ ./b2 -j$(nproc) --with-python install  
```  
  
I have also tried `--with-python=/usr/bin/python3.11` on bootstrap but that also doesn't seem to work.  
I have tried setting `LD_LIBRARY_PATH` to `/usr/local/lib` as well (where I installed Boost.Python) but that also didn't do the trick (I ran `ldconfig` and `ldconfig -n /usr/local/lib` too).  
  
Can anyone give us a hand?

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2024-05-17 18:42:53 UTC  
> Url: https://github.com/boostorg/python/issues/436#issuecomment-2118188250  

`Boost.Python` isn't a Python module you can import. It's a *library* you can use from your C++ project to *build* Python extension modules. See e.g. http://boostorg.github.io/python/doc/html/tutorial/tutorial/hello.html

---

## Comment 2

> Username: debemdeboas  
> Created at: 2024-05-17 18:58:28 UTC  
> Url: https://github.com/boostorg/python/issues/436#issuecomment-2118211033  

That's what I gathered from the documentation as well, but some of the team are adamant that they used to import Boost.Python in the past in `.pyi` files, like so:  
  
```python  
import Boost.Python  
  
class Foo(Boost.Python.Instance):  
    ...  
```

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2024-05-17 19:04:21 UTC  
> Url: https://github.com/boostorg/python/issues/436#issuecomment-2118218094  

They are mistaken...

---

## Comment 4

> Username: debemdeboas  
> Created at: 2024-05-17 19:09:11 UTC  
> Url: https://github.com/boostorg/python/issues/436#issuecomment-2118223874  

Ah, well. Thank you. In that case, I'll talk to them and see where these `.pyi` files came from. There may be a possibility that they were directly translated from C code without care.
