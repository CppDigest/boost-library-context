# #11 Fix compilation with WIN32_LEAN_AND_MEAN [Merged]

> Username: MarcelRaad  
> Created at: 2016-01-13 15:57:58 UTC  
> Updated at: 2016-01-13 20:19:19 UTC  
> Merged at: 2016-01-13 18:39:39 UTC  
> Closed at: 2016-01-13 18:39:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/11  

If WIN32_LEAN_AND_MEAN is defined, wincrypt.h is not included in windows.h.  
Always explicitly including wincrypt.h when BOOST_USE_WINDOWS_H is defined  
doesn't hurt anyway, as this is where the functions used here are defined.  
  
Tested with clang-cl and Windows SDK 10.

---
