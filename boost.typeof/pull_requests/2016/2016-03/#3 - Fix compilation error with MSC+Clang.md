# #3 Fix compilation error with MSC+Clang [Merged]

> Username: awulkiew  
> Created at: 2016-03-16 00:57:06 UTC  
> Updated at: 2017-04-15 22:20:30 UTC  
> Merged at: 2017-04-15 22:20:30 UTC  
> Closed at: 2017-04-15 22:20:30 UTC  
> Url: https://github.com/boostorg/typeof/pull/3  

Check `defined(__clang__)` explicitly because with MSVC+Clang `__GNUC__` is not defined.

---
