# #646 Add CMake Testing for Bilinear Uniform Interpolator [Merged]

> Username: mborland  
> Created at: 2021-06-25 18:10:46 UTC  
> Updated at: 2021-06-27 16:16:15 UTC  
> Merged at: 2021-06-27 04:39:09 UTC  
> Closed at: 2021-06-27 04:39:09 UTC  
> Url: https://github.com/boostorg/math/pull/646  

@NAThompson Some benign changes based off standalone testing.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2021-06-25 19:54:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/646#pullrequestreview-693150200  

📁 include/boost/math/interpolators/detail/bilinear_uniform_detail.hpp

```diff
  47 |         dy_ = dy;
  45 |-         using std::to_string;
  48 |+ 
```

> Username: NAThompson  
> Created_at: 2021-06-25 19:54:55 UTC  
> Updated_at: 2021-06-25 19:54:56 UTC  
> Url: https://github.com/boostorg/math/pull/646#discussion_r659004264  

Wait, if we don't have the `using std::to_string`, then we won't get the ADL for multiprecision types on the error message on line 50 correct?  
  
See https://github.com/boostorg/multiprecision/pull/339


---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-06-25 19:55:45 UTC  
> Url: https://github.com/boostorg/math/pull/646#issuecomment-868800551  

@mborland : Thanks for this PR!

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-06-25 22:34:42 UTC  
> Url: https://github.com/boostorg/math/pull/646#issuecomment-868865873  

@mborland: Looks like the build failed due to a network problem; got a meaningless diff to kick off another build?

---
