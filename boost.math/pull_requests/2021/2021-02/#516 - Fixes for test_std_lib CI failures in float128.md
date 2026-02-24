# #516 Fixes for test_std_lib CI failures in float128 [Merged]

> Username: mborland  
> Created at: 2021-02-03 16:17:12 UTC  
> Updated at: 2021-02-03 19:04:34 UTC  
> Merged at: 2021-02-03 18:47:15 UTC  
> Closed at: 2021-02-03 18:47:15 UTC  
> Url: https://github.com/boostorg/math/pull/516  

Fix for large number of CI failures in `test_std_lib` in float128 tests. [Example here](https://travis-ci.org/github/mborland/math/jobs/757277541#L873).

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-03 16:52:09 UTC  
> Url: https://github.com/boostorg/math/pull/516#issuecomment-772657211  

Good catch!  
  
The using declarations are superfluous but harmless: as the non-member functions will be found via ADL.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-03 18:29:01 UTC  
> Url: https://github.com/boostorg/math/pull/516#issuecomment-772723781  

@jzmaddock This is clean. It's nice to see the green check mark for CI showing up again

---
