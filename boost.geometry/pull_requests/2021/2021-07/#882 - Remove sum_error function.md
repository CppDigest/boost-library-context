# #882 Remove sum_error function [Merged]

> Username: vissarion  
> Created at: 2021-07-05 10:27:29 UTC  
> Updated at: 2021-07-06 14:10:57 UTC  
> Merged at: 2021-07-06 14:10:57 UTC  
> Closed at: 2021-07-06 14:10:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/882  

This PR removes `sum_error` function and use (the equivalent) `two_sum` instead. Therefore removing the g++11 C++20 warnings  reported in https://github.com/boostorg/geometry/commit/b9b0f85560e719278e8d4e9fdc7da998dd5c7857#commitcomment-53032770

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-05 11:10:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/882#pullrequestreview-699067194  

📁 test/formulas/inverse_cases_small_angles.hpp

```diff
  53 |         { 180, 0 },{ 1e-20, 0 },
  54 |-         { 20003931.45862544700503349304, -0.00000000000000000000, 180.00000000000000000000, 67125.61229850351810455322266, -1.00000000000000000000 },
  54 |+         { 20003931.45862544700503349304, -9.4938222885831061895e-19, 180.00000000000000000000, 67125.61229850351810455322266, -1.00000000000000000000 },
```

> Username: awulkiew  
> Created_at: 2021-07-05 11:10:28 UTC  
> Updated_at: 2021-07-05 11:10:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/882#discussion_r663846941  

So this does change the result. It looks like this is more precise than -0 was, correct?  
  
AFAIU the cause of this result change is not caused by the lack of `volatile` but by the fact the the calculation in `two_sum()` is done differently. We could notice the effect of optimization if we compared results of debug vs release builds. Have you checked the release (e.g. `b2 variant=release`)?  
  
Btw, these values are not strictly compared in the test right?

> Username: vissarion  
> Created_at: 2021-07-05 13:10:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/882#discussion_r663920533  

> So this does change the result. It looks like this is more precise than -0 was, correct?  
  
Yes.  
  
> AFAIU the cause of this result change is not caused by the lack of `volatile` but by the fact the the calculation in `two_sum()` is done differently. We could notice the effect of optimization if we compared results of debug vs release builds. Have you checked the release (e.g. `b2 variant=release`)?  
  
Indeed, the use of `volatile` didn't affect the results (tested with both `release` and `debug` mode).  
  
> Btw, these values are not strictly compared in the test right?  
  
Yes, tests are passing if you replace `0.0000001` with `1e-14` in https://github.com/boostorg/geometry/blob/develop/test/formulas/inverse_karney.cpp#L91 and start failing with `1e-15`. This is the case for both release and debug, with and without volatile, `sum_error` and `two_sum` functions (apart from the one case fixed above).


---

## Comment 2

> Username: awulkiew  
> Created_at: 2021-07-06 14:10:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/882#issuecomment-874794980  

This fix is simple and doesn't affect the library so I'll merge it now.

---
