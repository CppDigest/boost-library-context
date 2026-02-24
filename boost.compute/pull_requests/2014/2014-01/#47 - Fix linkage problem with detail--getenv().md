# #47 Fix linkage problem with detail::getenv() [Merged]

> Username: ddemidov  
> Created at: 2014-01-07 17:33:34 UTC  
> Updated at: 2014-07-27 06:24:07 UTC  
> Merged at: 2014-01-07 18:10:14 UTC  
> Closed at: 2014-01-07 18:10:14 UTC  
> Url: https://github.com/boostorg/compute/pull/47  

`detail::getenv()` function was not declared `inline`, which led to `multiple definition` errors at link time when a program consisted of multiple objects that included Boost.Compute headers.  
  
Fixed the problem and added `core.multiple_objects` test.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-01-07 18:10:16 UTC  
> Url: https://github.com/boostorg/compute/pull/47#issuecomment-31762759  

Looks good! Merged!

---
