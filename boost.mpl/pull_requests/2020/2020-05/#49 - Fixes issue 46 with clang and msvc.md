# #49 Fixes issue 46 with clang and msvc [Merged]

> Username: matbech  
> Created at: 2020-05-09 05:54:26 UTC  
> Updated at: 2022-05-01 20:07:58 UTC  
> Merged at: 2022-05-01 20:07:58 UTC  
> Closed at: 2022-05-01 20:07:58 UTC  
> Url: https://github.com/boostorg/mpl/pull/49  

Fixes issue https://github.com/boostorg/mpl/issues/46

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-09-25 19:43:42 UTC  
> Url: https://github.com/boostorg/mpl/pull/49#issuecomment-699119908  

If you close this PR and reopen it the tests should now run correctly. But I need some clang on Windows command line and some simple test code which shows the bug you are trying to fix. If I run the mpl tests with clang on Windows 10.0, all tests currently pass without errors. If you see errors in the clang on Windows tests, please post your toolset definition for clang on Windows.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2022-05-01 14:46:19 UTC  
> Url: https://github.com/boostorg/mpl/pull/49#issuecomment-1114256126  

# [Codecov](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#49](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (d8f8e2e) into [develop](https://codecov.io/gh/boostorg/mpl/commit/3301d917dc04feef102c3cfce73c6c3e760a678a?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (3301d91) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/mpl/pull/49/graphs/tree.svg?width=650&height=150&src=pr&token=31bBblRchG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #49   +/-   ##  
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
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3301d91...d8f8e2e](https://codecov.io/gh/boostorg/mpl/pull/49?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).

---
