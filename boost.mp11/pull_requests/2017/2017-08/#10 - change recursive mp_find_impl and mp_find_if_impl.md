# #10 change recursive mp_find_impl and mp_find_if_impl [Closed]

> Username: robhz786  
> Created at: 2017-08-07 19:40:39 UTC  
> Updated at: 2017-08-07 22:39:25 UTC  
> Closed at: 2017-08-07 22:30:57 UTC  
> Url: https://github.com/boostorg/mp11/pull/10  

propose to replace the recurse mp_find and mp_find_if.  
In visual studio 2013, the proposed implementation perfom worse if mp_find is called only once, but better if called more than twice. It also supports larger lists

---
