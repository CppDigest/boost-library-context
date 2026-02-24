# #369 - Avoid memory fence when fetching error category [Closed]

> Username: vinniefalco  
> Created at: 2017-05-19 15:19:07 UTC  
> Updated at: 2017-08-16 00:15:44 UTC  
> Closed at: 2017-08-16 00:15:44 UTC  
> Url: https://github.com/boostorg/beast/issues/369  

https://github.com/ned14/boost.outcome/blob/4851926c1c601c0e9391b2d0d07fb17f634b3ecb/include/boost/outcome/v1.0/error_code_extended.hpp#L45  
  
Also, see discussion on boost-dev

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 01:17:20 UTC  
> Url: https://github.com/boostorg/beast/issues/369#issuecomment-309935059  

Fastest way to clear an error:  
```  
ec.assign(0, ec.cateogory());  
```  
This avoids the synchronization

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-16 00:15:44 UTC  
> Url: https://github.com/boostorg/beast/issues/369#issuecomment-322623494  

Frivolous.
