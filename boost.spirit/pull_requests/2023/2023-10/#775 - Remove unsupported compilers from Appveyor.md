# #775 Remove unsupported compilers from Appveyor [Merged]

> Username: Flamefire  
> Created at: 2023-10-08 10:12:20 UTC  
> Updated at: 2023-10-09 07:38:32 UTC  
> Merged at: 2023-10-08 23:07:28 UTC  
> Closed at: 2023-10-08 23:07:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/775  

msvc-10/11/12 is no longer supported by Boost.Variant hence Spirit cannot be used (or at least tested) with those.  
So remove them.

---
