# #512 - CI Failures with distribution_tests [Closed]

> Username: mborland  
> Created at: 2021-02-02 16:57:23 UTC  
> Updated at: 2021-08-17 19:07:51 UTC  
> Closed at: 2021-08-17 19:07:51 UTC  
> Url: https://github.com/boostorg/math/issues/512  

Several test failures on distribution_tests using GCC-9, CXXSTD=17, and ARCH=ARM64. Many of these seem related to long double.  
  
[hypergeometric cdf complement](https://travis-ci.org/github/mborland/math/jobs/756971712#L1076)  
[logistic dist](https://travis-ci.org/github/mborland/math/jobs/756971712#L1204)  
[non-central t cdf](https://travis-ci.org/github/mborland/math/jobs/756971712#L1441)  
[skew normal](https://travis-ci.org/github/mborland/math/jobs/756971712#L1533)  
[students t](https://travis-ci.org/github/mborland/math/jobs/756971712#L1625)

---

## Comment 1

> Username: mborland  
> Created at: 2021-02-02 17:18:02 UTC  
> Url: https://github.com/boostorg/math/issues/512#issuecomment-771808367  

[Similar errors appear on ARCH=S390X](https://travis-ci.org/github/mborland/math/jobs/756971719#L1529)
