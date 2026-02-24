# #30 Fix MSVC-11 compilation error [Merged]

> Username: Lastique  
> Created at: 2020-02-05 20:10:13 UTC  
> Updated at: 2020-02-10 08:45:28 UTC  
> Merged at: 2020-02-10 05:01:11 UTC  
> Closed at: 2020-02-10 05:01:11 UTC  
> Url: https://github.com/boostorg/exception/pull/30  

The compiler supports `std::exception_ptr` but does not support uniform initialization syntax.  
  
Compiler error can be seen in Boost.Log [tests](https://ci.appveyor.com/project/Lastique/log/builds/30590466/job/n7d00mf8exhtuing#L734).

---

## Comment 1

> Username: jefftrull  
> Created_at: 2020-02-10 00:15:47 UTC  
> Url: https://github.com/boostorg/exception/pull/30#issuecomment-583912124  

I observe this problem in Boost.Wave CI builds and would be glad to have this fix.

---
