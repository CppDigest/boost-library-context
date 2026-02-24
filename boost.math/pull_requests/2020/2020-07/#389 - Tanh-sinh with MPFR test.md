# #389 Tanh-sinh with MPFR test. [Merged]

> Username: NAThompson  
> Created at: 2020-07-03 16:04:50 UTC  
> Updated at: 2020-07-04 17:09:38 UTC  
> Merged at: 2020-07-04 17:09:20 UTC  
> Closed at: 2020-07-04 17:09:20 UTC  
> Url: https://github.com/boostorg/math/pull/389  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-07-03 19:07:11 UTC  
> Url: https://github.com/boostorg/math/pull/389#issuecomment-653653250  

There's something strange going on in the original test case - I don't see why the checks in itrunc are failing?  So this might be an issue with mpfr_float....

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-07-03 19:10:23 UTC  
> Url: https://github.com/boostorg/math/pull/389#issuecomment-653653845  

@jzmaddock : I noticed that as well, but concluded it was a separate issue. In any case, I realized that `itrunc` really needed to be a `lltrunc` in order to match the size of the lhs . . .

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2020-07-03 19:13:34 UTC  
> Url: https://github.com/boostorg/math/pull/389#issuecomment-653654458  

Nah, my bad.  It's overflow in `sqrt((2 - x) / x` that's the issue.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-07-04 17:09:38 UTC  
> Url: https://github.com/boostorg/math/pull/389#issuecomment-653789904  

The build failures come from the known issue in serialization.

---
