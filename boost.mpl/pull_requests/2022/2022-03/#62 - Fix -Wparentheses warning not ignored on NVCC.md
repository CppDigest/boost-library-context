# #62 Fix -Wparentheses warning not ignored on NVCC [Merged]

> Username: aladram  
> Created at: 2022-03-10 20:21:32 UTC  
> Updated at: 2022-05-01 12:47:52 UTC  
> Merged at: 2022-05-01 12:47:52 UTC  
> Closed at: 2022-05-01 12:47:52 UTC  
> Url: https://github.com/boostorg/mpl/pull/62  

BOOST_GCC is not defined for NVCC, therefore we should use BOOST_GCC_VERSION.

---

## Comment 1

> Username: aladram  
> Created_at: 2022-03-10 22:46:01 UTC  
> Url: https://github.com/boostorg/mpl/pull/62#issuecomment-1064583886  

@mclow could you please look at the Windows CI results? Failure seems to come from a package missing from the msys repo (pacman 5.2.1-12). This change only affects GCC + NVCC. Thanks!

---

## Comment 2

> Username: aladram  
> Created_at: 2022-03-18 16:38:18 UTC  
> Url: https://github.com/boostorg/mpl/pull/62#issuecomment-1072587643  

@eldiener any chance you could look at this PR when you have some time? Thanks!

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2022-05-01 04:01:11 UTC  
> Updated_at: 2022-05-01 12:31:40 UTC  
> Url: https://github.com/boostorg/mpl/pull/62#issuecomment-1114121987  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#62](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (b076018) into [develop](https://codecov.io/gh/boostorg/mpl/commit/c61b1e350845ba9b12201768945379dc2e716e8f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c61b1e3) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/62/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #62   +/-   ##  
========================================  
  Coverage    94.73%   94.73%             
========================================  
  Files            5        5             
  Lines           19       19             
========================================  
  Hits            18       18             
  Misses           1        1             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c61b1e3...b076018](https://codecov.io/gh/boostorg/mpl/pull/62?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 4

> Username: jeking3  
> Created_at: 2022-05-01 11:36:41 UTC  
> Url: https://github.com/boostorg/mpl/pull/62#issuecomment-1114211167  

CI failure is known and tracked by #50

---

## Review 5 [Changes requested]

> Username: jeking3  
> Created_at: 2022-05-01 11:49:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/mpl/pull/62#pullrequestreview-958579143  

I would suggest that https://github.com/boostorg/mpl/pull/34 should have used BOOST_WORKAROUND based on work done 5 years prior to standardize on that macro, so this is a good opportunity to do the same and remedy that.  I will push a fix.

📁 include/boost/mpl/assert.hpp

```diff
 186 | 
 187 |- #if defined(BOOST_GCC) && BOOST_GCC >= 80000
 187 |+ #if defined(BOOST_GCC_VERSION) && BOOST_GCC_VERSION >= 80000
```

> Username: jeking3  
> Created_at: 2022-05-01 11:45:02 UTC  
> Updated_at: 2022-05-01 11:49:31 UTC  
> Url: https://github.com/boostorg/mpl/pull/62#discussion_r862462697  

```diff  
-#if defined(BOOST_GCC_VERSION) && BOOST_GCC_VERSION >= 80000  
+#if BOOST_WORKAROUND(BOOST_GCC_VERSION, >= 80000)  
```


---
