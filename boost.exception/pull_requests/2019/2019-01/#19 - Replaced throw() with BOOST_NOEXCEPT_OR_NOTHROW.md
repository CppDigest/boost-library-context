# #19 Replaced throw() with BOOST_NOEXCEPT_OR_NOTHROW [Merged]

> Username: jlepola  
> Created at: 2019-01-28 07:52:18 UTC  
> Updated at: 2019-03-03 06:10:35 UTC  
> Merged at: 2019-03-03 06:08:47 UTC  
> Closed at: 2019-03-03 06:08:47 UTC  
> Url: https://github.com/boostorg/exception/pull/19  

With throw(), Visual Studio 2017 emitted a warning "C26439 SPECIAL_NOEXCEPT". Reproducible at least if code analysis was set to level: "Microsoft Native Recommended Rules".  
  
https://docs.microsoft.com/en-us/visualstudio/code-quality/c26439?view=vs-2017

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-02-24 12:09:54 UTC  
> Url: https://github.com/boostorg/exception/pull/19#issuecomment-466768353  

Even worse - IIRC throw() is going to be removed completely from the standard in c++20

---

## Comment 2

> Username: zajo  
> Created_at: 2019-03-03 06:10:35 UTC  
> Url: https://github.com/boostorg/exception/pull/19#issuecomment-468992685  

Thank you!

---
