# #172 Incorrect MSVC version detection [Merged]

> Username: sergey-shandar  
> Created at: 2017-08-09 00:37:30 UTC  
> Updated at: 2017-08-11 18:21:51 UTC  
> Merged at: 2017-08-11 18:20:34 UTC  
> Closed at: 2017-08-11 18:20:34 UTC  
> Url: https://github.com/boostorg/config/pull/172  

I know, Boost doesn't support MSVC 5.0 and 6.0. But the expression is invalid. Another way to fix it is  
```c++  
#   if _MSC_VER < 1300  
      // Note: Versions up to 7.0 aren't supported.  
#     define BOOST_COMPILER_VERSION 6.0  
#   elif _MSC_VER < 1310  
#     define BOOST_COMPILER_VERSION 7.0  
```

---
