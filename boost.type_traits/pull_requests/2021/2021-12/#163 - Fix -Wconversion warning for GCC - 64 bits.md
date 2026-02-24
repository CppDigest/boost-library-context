# #163 Fix -Wconversion warning for GCC / 64 bits [Merged]

> Username: igaztanaga  
> Created at: 2021-12-30 14:45:30 UTC  
> Updated at: 2022-03-02 18:07:48 UTC  
> Merged at: 2022-02-10 13:10:42 UTC  
> Closed at: 2022-02-10 13:10:42 UTC  
> Url: https://github.com/boostorg/type_traits/pull/163  

../../boost/type_traits/is_complete.hpp:47:14: error: conversion from ‘long unsigned int’ to ‘unsigned int’ may change value   
       ok_tag<sizeof(T)> check_is_complete(int);

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-03-02 17:35:52 UTC  
> Url: https://github.com/boostorg/type_traits/pull/163#issuecomment-1057195709  

Can we please have this merged to master?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-03-02 18:07:48 UTC  
> Url: https://github.com/boostorg/type_traits/pull/163#issuecomment-1057224782  

Done.

---
