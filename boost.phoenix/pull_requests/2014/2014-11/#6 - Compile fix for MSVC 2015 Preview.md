# #6 Compile fix for MSVC 2015 Preview [Merged]

> Username: MarcelRaad  
> Created at: 2014-11-26 22:27:16 UTC  
> Updated at: 2014-11-27 06:25:39 UTC  
> Merged at: 2014-11-27 00:15:27 UTC  
> Closed at: 2014-11-27 00:15:27 UTC  
> Url: https://github.com/boostorg/phoenix/pull/6  

hash_set and hash_map are deprecated in Visual C++ 2015 and result in a compile error by default. BOOST_DINKUMWARE_STDLIB is still 610.

---
