# #513 - CI Failures with long doubles in misc tests on ARM64 [Closed]

> Username: mborland  
> Created at: 2021-02-02 17:15:20 UTC  
> Updated at: 2021-08-17 19:07:38 UTC  
> Closed at: 2021-08-17 19:07:38 UTC  
> Url: https://github.com/boostorg/math/issues/513  

Several long double related test failures with GCC-9 CXXSTD=17 and ARCH=ARM64  
  
[test_constants](https://travis-ci.org/github/mborland/math/jobs/756971717#L1057)  
[std::exp](https://travis-ci.org/github/mborland/math/jobs/756971717#L1236)  
[cohen_acceleration](https://travis-ci.org/github/mborland/math/jobs/756971717#L1551)  
[cardinal_quadratic_b_spline](https://travis-ci.org/github/mborland/math/jobs/756971717#L1603)  
[whittaker_shannon](https://travis-ci.org/github/mborland/math/jobs/756971717#L1659)  
[linear_regression](https://travis-ci.org/github/mborland/math/jobs/756971717#L13980)

---

## Comment 1

> Username: mborland  
> Created at: 2021-02-02 17:19:46 UTC  
> Url: https://github.com/boostorg/math/issues/513#issuecomment-771809925  

[Similar errors with ARCH=S390X](https://travis-ci.org/github/mborland/math/jobs/756971724#L1598)

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-08-17 05:10:13 UTC  
> Url: https://github.com/boostorg/math/issues/513#issuecomment-899997996  

Did this issue get fixed?

---

## Comment 3

> Username: mborland  
> Created at: 2021-08-17 19:07:38 UTC  
> Url: https://github.com/boostorg/math/issues/513#issuecomment-900559476  

I haven't tried anything on travis since the migration happened. I can close these other architecture issues because we have no real way of testing them.
