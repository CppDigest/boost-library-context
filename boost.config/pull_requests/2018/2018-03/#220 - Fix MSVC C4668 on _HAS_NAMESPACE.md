# #220 Fix MSVC C4668 on _HAS_NAMESPACE [Merged]

> Username: pkolbus  
> Created at: 2018-03-27 03:50:25 UTC  
> Updated at: 2018-04-18 18:21:30 UTC  
> Merged at: 2018-04-18 18:21:30 UTC  
> Closed at: 2018-04-18 18:21:30 UTC  
> Url: https://github.com/boostorg/config/pull/220  

This PR resolves an instance of MSVC warning C4668 (undefined preprocessor macro) for _HAS_NAMESPACE, by checking for Green Hills compiler first.

---
