# #86 Fix Windows version problems [Merged]

> Username: MarcelRaad  
> Created at: 2016-05-25 13:58:25 UTC  
> Updated at: 2016-05-25 22:49:35 UTC  
> Merged at: 2016-05-25 21:26:16 UTC  
> Closed at: 2016-05-25 21:26:16 UTC  
> Url: https://github.com/boostorg/thread/pull/86  

- the target Windows version was hardcoded to Windows 95  
  in thread.cpp if not already defined  
- the include for BOOST_USE_WINAPI_VERSION was missing  
  
Tested with Visual Studio 2015.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-05-25 21:26:00 UTC  
> Url: https://github.com/boostorg/thread/pull/86#issuecomment-221712574  

I'm unable to check on windows. Hopping this will not introduce a regressions elsewhere.

---
