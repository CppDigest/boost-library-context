# #11 change recursive mp_find_impl and mp_find_if_impl [Closed]

> Username: robhz786  
> Created at: 2017-08-07 22:42:44 UTC  
> Updated at: 2017-10-23 15:27:13 UTC  
> Closed at: 2017-10-23 15:27:13 UTC  
> Url: https://github.com/boostorg/mp11/pull/11  

A propose to replace the recursive version of mp_find and mp_find_if.  
In visual studio 2013, the proposed implementation perfom worse if mp_find is called only once, but better if called more than twice. It also supports larger lists  
  
I'm not really sure whether  it worths. Up to you to decide.

---
