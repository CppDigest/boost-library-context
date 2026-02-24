# #564 Fix up non-central T test data for 128-bit floats. [Merged]

> Username: jzmaddock  
> Created at: 2021-03-01 12:06:34 UTC  
> Updated at: 2021-03-09 13:03:11 UTC  
> Merged at: 2021-03-09 12:11:04 UTC  
> Closed at: 2021-03-09 12:11:04 UTC  
> Url: https://github.com/boostorg/math/pull/564  

Fixes: https://github.com/boostorg/math/issues/544.  
Also fixes uncovered issue in tgamma_ratio where we were previously relying on sub-normals to get the correct result.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-03-01 14:25:58 UTC  
> Url: https://github.com/boostorg/math/pull/564#issuecomment-787988597  

I believe this is the first time I've ever heard someone say subnormals actually did something useful.  
  
Wowsers!

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-03-09 12:10:57 UTC  
> Url: https://github.com/boostorg/math/pull/564#issuecomment-793795892  

Failure are GHA SNAFU's, merging.

---
