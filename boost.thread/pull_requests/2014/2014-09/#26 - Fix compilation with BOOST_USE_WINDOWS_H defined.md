# #26 Fix compilation with BOOST_USE_WINDOWS_H defined [Merged]

> Username: Lastique  
> Created at: 2014-09-30 18:18:04 UTC  
> Updated at: 2014-09-30 18:38:35 UTC  
> Merged at: 2014-09-30 18:38:35 UTC  
> Closed at: 2014-09-30 18:38:35 UTC  
> Url: https://github.com/boostorg/thread/pull/26  

Don't use macros defined only in Windows SDK 6.0 and later as this breaks compilation with MinGW and older MSVC.

---
