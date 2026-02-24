# #452 Fix up Python2 regressions in PR 417 [Merged]

> Username: jvansanten  
> Created at: 2024-09-18 13:32:42 UTC  
> Updated at: 2024-09-19 01:54:20 UTC  
> Merged at: 2024-09-18 14:36:45 UTC  
> Closed at: 2024-09-18 14:36:45 UTC  
> Url: https://github.com/boostorg/python/pull/452  

boostorg/python#417 introduced some regressions with Python2. To whit:  
  
- `add_property()` attempts to add docstrings to functions passed to it. `test.properties` [binds an existing instance method again as a getter](https://github.com/boostorg/python/blob/c76d67ef3fe81eb4177cfcaa2d6486395e3aed75/test/properties.cpp#L97), which works fine on Python3, where `klass.member` returns the underlying function object. On Python2, however, this returned an unbound `instancemethod` with an immutable `__doc__` attribute. Work around this by skipping docstring generation on `PyMethod` objects.  
- `test.nested` exercises the `__qualname__` attribute, which didn't exist before Python 3.3. Use doctest option flags to make examples conditional on the Python version.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2024-09-19 01:54:18 UTC  
> Url: https://github.com/boostorg/python/pull/452#issuecomment-2359817154  

Thanks @jvansanten , the `develop` branch is stable again. I intend to merge `develop` to `master` shortly, so all of that work should be part of the next boost release. Thanks again for your contribution !

---
