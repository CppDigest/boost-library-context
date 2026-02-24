# #613 Fixes for gnu dialect quadrature failures [Merged]

> Username: mborland  
> Created at: 2021-04-24 09:22:32 UTC  
> Updated at: 2021-04-26 17:55:24 UTC  
> Merged at: 2021-04-25 14:23:54 UTC  
> Closed at: 2021-04-25 14:23:54 UTC  
> Url: https://github.com/boostorg/math/pull/613  

Reverts the const to constexpr conversions for arrays in float128 branches.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-04-24 13:43:38 UTC  
> Url: https://github.com/boostorg/math/pull/613#issuecomment-826095253  

Wait, I thought this was a nice change; what failed?

---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-24 14:36:31 UTC  
> Url: https://github.com/boostorg/math/pull/613#issuecomment-826102190  

> Wait, I thought this was a nice change; what failed?  
  
The majority of the changes remained `constexpr`. The ones enabled/disabled by `#ifdef BOOST_HAS_FLOAT128` had to be reverted. [The error ](https://drone.cpp.al/boostorg/math/176/137/2)was related to multiprecision types not being literals usable in constexpr contexts.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-04-25 11:33:52 UTC  
> Url: https://github.com/boostorg/math/pull/613#issuecomment-826309005  

This should be good to go. [The one CI failure](https://github.com/boostorg/math/pull/613/checks?check_run_id=2426116554#step:18:376) looks spurious.

---
