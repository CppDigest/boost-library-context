# #545 - thread_local is not supported on some 32bit platforms [Closed]

> Username: theodelrieu  
> Created at: 2021-04-23 15:35:57 UTC  
> Updated at: 2021-05-22 18:19:54 UTC  
> Closed at: 2021-05-22 18:19:54 UTC  
> Url: https://github.com/boostorg/json/issues/545  

### Version of Boost  
  
1.76.0  
  
### Steps necessary to reproduce the problem  
  
Single main including `boost/json.hpp`, building for iOS simulator x86 (32-bit)  
  
### All relevant compiler information  
  
I do get the following error:  
  
>./boost/json/detail/ryu/impl/d2s.ipp:733:12: error: thread-local storage is not supported for the current target  
>   static thread_local char result[25];

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-04-23 15:37:43 UTC  
> Url: https://github.com/boostorg/json/issues/545#issuecomment-825742575  

If anyone is affected by this bug there are two workarounds for Boost 1.76 and earlier:  
  
1. Remove the definition of d2s() by changing the file d2s.ipp in the Boost sources, or  
  
2. Use the header-only configuration, and add the following line before including src.hpp:  
```  
#define thread_local /**/  
```  
  
Boost 1.77 and later will have this resolved.
