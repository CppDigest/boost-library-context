# #269 Make __module__ on function objects consistent with cpython [Closed]

> Username: shkoo  
> Created at: 2019-06-03 04:05:44 UTC  
> Updated at: 2022-06-10 19:44:26 UTC  
> Closed at: 2022-06-10 19:44:26 UTC  
> Url: https://github.com/boostorg/python/pull/269  

This changes the `__module__` attribute of member functions to indicate the module the class is defined in rather than the name of the class.  
  
This new behavior is consistent with cpython's handling of pure python member functions.

---
