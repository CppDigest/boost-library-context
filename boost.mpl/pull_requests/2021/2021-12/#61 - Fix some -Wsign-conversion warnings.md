# #61 Fix some -Wsign-conversion warnings [Merged]

> Username: igaztanaga  
> Created at: 2021-12-31 00:00:40 UTC  
> Updated at: 2022-05-01 02:45:39 UTC  
> Merged at: 2022-05-01 02:45:39 UTC  
> Closed at: 2022-05-01 02:45:39 UTC  
> Url: https://github.com/boostorg/mpl/pull/61  

Detect while using boost::transform_iterator in a Boost.Container test

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-12-31 12:29:32 UTC  
> Url: https://github.com/boostorg/mpl/pull/61#issuecomment-1003365116  

Looks like there were some errors in the regression not related to the patch:  
  
gcc.link bin.v2/libs/mpl/test/string.test/gcc-8/debug/cxxstd-2a-iso/threading-multi/visibility-hidden/string  
g++-8: error: linkflags=-fno-sanitize-recover=undefined: No such file or directory  
g++-8: error: linkflags=-fuse-ld=gold: No such file or directory

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-01 02:40:01 UTC  
> Updated_at: 2022-05-01 02:43:51 UTC  
> Url: https://github.com/boostorg/mpl/pull/61#issuecomment-1114109085  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#61](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7ba0074) into [develop](https://codecov.io/gh/boostorg/mpl/commit/e9e3faf4be3ad8c8f153024b9e8e2b77c8bcf223?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (e9e3faf) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/61/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #61   +/-   ##  
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
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e9e3faf...7ba0074](https://codecov.io/gh/boostorg/mpl/pull/61?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-05-01 02:45:35 UTC  
> Url: https://github.com/boostorg/mpl/pull/61#issuecomment-1114109738  

Looks okay, only issue is known (#50).

---
