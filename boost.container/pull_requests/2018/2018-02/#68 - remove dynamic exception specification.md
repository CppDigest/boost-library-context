# #68 remove dynamic exception specification [Closed]

> Username: DanielaE  
> Created at: 2018-02-01 17:08:18 UTC  
> Updated at: 2018-03-03 09:05:26 UTC  
> Closed at: 2018-02-04 09:24:45 UTC  
> Url: https://github.com/boostorg/container/pull/68  

They're no longer allowed in C++17, they were deprecated in C++11, and they were considered bad practice even before by most people. It's use causes a compile error in CI.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Commented]

> Username: igaztanaga  
> Created_at: 2018-02-01 20:26:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/container/pull/68#pullrequestreview-93425691  

📁 test/global_resource_test.cpp

```diff
  29 | 
  30 | #if defined(BOOST_GCC) && (BOOST_GCC >= 40700) && (__cplusplus >= 201103L)
  31 |- #define BOOST_CONTAINER_NEW_EXCEPTION_SPECIFIER
```

> Username: igaztanaga  
> Created_at: 2018-02-01 20:26:41 UTC  
> Url: https://github.com/boostorg/container/pull/68#discussion_r165477915  

This triggers warnings in older platforms that Boost.Container still supports. I guess this should be detected depending on the compiler version and maybe added to Boost.Config.

> Username: igaztanaga  
> Created_at: 2018-02-01 20:53:33 UTC  
> Url: https://github.com/boostorg/container/pull/68#discussion_r165484245  

Current behavior is only for GCC, in commit:  
  
https://github.com/boostorg/container/commit/485878d566c21d6029129769019ed96e0214b537  
  
the exception specifier is not used if __cplusplus shows C++11.

> Username: DanielaE  
> Created_at: 2018-02-04 09:24:13 UTC  
> Updated_at: 2018-02-04 09:24:14 UTC  
> Url: https://github.com/boostorg/container/pull/68#discussion_r165838315  

Great, thanks!  
  
Now, due to your change, my PR#66 has stopped failing the CI tests on clang.


---
