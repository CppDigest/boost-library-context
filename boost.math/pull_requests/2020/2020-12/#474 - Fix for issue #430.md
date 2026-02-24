# #474 Fix for issue #430 [Merged]

> Username: mborland  
> Created at: 2020-12-27 17:15:16 UTC  
> Updated at: 2021-01-07 16:35:35 UTC  
> Merged at: 2020-12-30 18:56:24 UTC  
> Closed at: 2020-12-30 18:56:24 UTC  
> Url: https://github.com/boostorg/math/pull/474  

Fix for issue #430 . Added explicit casting to eliminate `-Wimplicit-int-float-conversion` warnings on clang. Added test case of `std::numeric_limits<long long>::max() + 1` which passes ubsan.

---

## Comment 1

> Username: mborland  
> Created_at: 2020-12-30 13:30:37 UTC  
> Url: https://github.com/boostorg/math/pull/474#issuecomment-752602925  

@jzmaddock The CI on this one is clean and should be good to go.

---
