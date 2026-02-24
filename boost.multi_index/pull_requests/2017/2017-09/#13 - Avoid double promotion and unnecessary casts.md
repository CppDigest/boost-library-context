# #13 Avoid double promotion and unnecessary casts  [Merged]

> Username: ecatmur  
> Created at: 2017-09-19 17:18:46 UTC  
> Updated at: 2017-09-21 07:49:14 UTC  
> Merged at: 2017-09-21 07:49:14 UTC  
> Closed at: 2017-09-21 07:49:14 UTC  
> Url: https://github.com/boostorg/multi_index/pull/13  

When performing max load factor calculations, cast operands to float (not double), perform operations in float, and don't cast result to float if it is already in float. Fixes warnings with -Wdouble-promotion and -Wuseless-cast.

---
