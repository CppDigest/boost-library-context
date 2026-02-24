# #517 Fix for unable to find numeric literal operator Q [Merged]

> Username: mborland  
> Created at: 2021-02-03 17:16:54 UTC  
> Updated at: 2021-02-25 16:44:02 UTC  
> Merged at: 2021-02-13 19:15:35 UTC  
> Closed at: 2021-02-13 19:15:35 UTC  
> Url: https://github.com/boostorg/math/pull/517  

Disable several tests in `test_cstdfloat` if GNU extensions are not enabled on GCC. Several issues across other repos in github that point back to this. Example in CI [here](https://travis-ci.org/github/mborland/math/jobs/757277544#L1991).

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-03 17:53:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/517#pullrequestreview-582642611  

📁 test/test_cstdfloat.cpp

```diff
 306 |       val = 2.5;
 307 |+ 
 308 |+       #ifndef __STRICT_ANSI__
```

> Username: jzmaddock  
> Created_at: 2021-02-03 17:53:37 UTC  
> Updated_at: 2021-02-03 18:47:36 UTC  
> Url: https://github.com/boostorg/math/pull/517#discussion_r569624571  

We have a macro `BOOST_MATH_LARGEST_FLOAT_C(val)` declared in boost/math/tools/big_constant.hpp which declares `val` as the largest supported literal.  
  
But there's another issue here, the function test() appears to be never called?  @ckormanyos ?


---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-02-03 18:48:57 UTC  
> Updated_at: 2021-02-03 18:52:08 UTC  
> Url: https://github.com/boostorg/math/pull/517#issuecomment-772735890  

> the function test() appears to be never called?   
  
It appears as though when `BOOST_FLOAT128_C` is defined, then `test_cstdfloat::extend_check_128_func()` near the bottom of the main test routine should subsequently call `test<boost::float128_t>()`.  
  
If memory serves, then `BOOST_FLOAT128_C` is automatically set when the compiler can detect the presence of an existing 128-bit built in floating-point type such as `__float128` in the well-known `quadmath.h` from the GCC world.  
  
We wrote this code, however, quite a long time ago, so it would not really surprise me if the logic on some of those compiler switches needs to be adusted or re-adjusted. I can try a few dedicated compilation runs here at my workstation in a standalone project and report findings.  
  
So, as we noted on the previous weekend, if GNU extensions are not enabled on GCC, then I suppose this function will not be called because it will not be present within the translation unit of `test_cstdfloat.cpp` (because those calls are wrapped with the need for `BOOST_FLOAT128_C`).

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-02-04 07:16:24 UTC  
> Url: https://github.com/boostorg/math/pull/517#issuecomment-773089596  

> It appears as though when `BOOST_FLOAT128_C` is defined, ...  
  
Or is the use of `BOOST_FLOAT128_C` not optimal for reaching Boolean decisions on preprocessor level? Years ago we had some dialog regarding which preprocessor definition to use for activating/deactivating the quad-math stuff and i think we decided for that one.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-02-04 18:30:05 UTC  
> Url: https://github.com/boostorg/math/pull/517#issuecomment-773516196  

This does resolve the errors. New run with this commit [here](https://travis-ci.org/github/mborland/math/jobs/757568362).

---
