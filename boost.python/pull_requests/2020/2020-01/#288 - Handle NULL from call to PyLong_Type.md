# #288 Handle NULL from call to PyLong_Type [Merged]

> Username: ausmal  
> Created at: 2020-01-29 00:18:45 UTC  
> Updated at: 2020-08-08 06:30:47 UTC  
> Merged at: 2020-08-08 06:30:47 UTC  
> Closed at: 2020-08-08 06:30:47 UTC  
> Url: https://github.com/boostorg/python/pull/288  

PyLong_Type raises an exception if the argument is not convertible to  
long, therefore, this has to be handled as new_reference and not  
new_non_null_reference, otherwise a segfault will occur.

---
