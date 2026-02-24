# #669 fuzz parsing functions [Merged]

> Username: alandefreitas  
> Created at: 2023-01-25 19:09:59 UTC  
> Updated at: 2023-01-28 01:46:08 UTC  
> Merged at: 2023-01-28 01:46:07 UTC  
> Closed at: 2023-01-28 01:46:07 UTC  
> Url: https://github.com/boostorg/url/pull/669  

The fuzzer is working. Boost.JSON is running it on GHA but I'm not sure we should replicate this way.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-01-25 19:21:21 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1404116430  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-01-25 22:45:08 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1404323376  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-01-25 23:24:02 UTC  
> Updated_at: 2023-01-27 22:55:21 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1404353489  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#669](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (adc61c6) into [develop](https://codecov.io/gh/boostorg/url/commit/0c040b546fbcb26cf9fa15b4d52d1942706f4148?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (0c040b5) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
> :exclamation: Current head adc61c6 differs from pull request most recent head 099ff4a. Consider uploading reports for the commit 099ff4a to get more accurate results  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/669/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #669   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          152      152             
  Lines         8441     8441             
========================================  
  Hits          8199     8199             
  Misses         242      242             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0c040b5...099ff4a](https://codecov.io/gh/boostorg/url/pull/669?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-01-25 23:25:55 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1404354726  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2023-01-26 17:25:10 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405346597  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-01-26 17:50:34 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405376139  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-01-26 18:45:29 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405444738  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 8

> Username: cppalliance-bot  
> Created_at: 2023-01-26 22:20:53 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405749065  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 9

> Username: cppalliance-bot  
> Created_at: 2023-01-26 22:56:07 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405780315  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 10

> Username: cppalliance-bot  
> Created_at: 2023-01-26 23:08:56 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405793284  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 11

> Username: cppalliance-bot  
> Created_at: 2023-01-26 23:21:39 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405804469  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 12

> Username: cppalliance-bot  
> Created_at: 2023-01-27 02:35:19 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405945328  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 13

> Username: cppalliance-bot  
> Created_at: 2023-01-27 03:04:35 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1405960369  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 14

> Username: cppalliance-bot  
> Created_at: 2023-01-27 17:46:11 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1406861290  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 15

> Username: cppalliance-bot  
> Created_at: 2023-01-27 22:36:16 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1407153564  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 16

> Username: cppalliance-bot  
> Created_at: 2023-01-27 23:11:32 UTC  
> Url: https://github.com/boostorg/url/pull/669#issuecomment-1407179780  

GCOVR code coverage report [https://669.url.prtest.cppalliance.org/gcovr/index.html](https://669.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://669.url.prtest.cppalliance.org/genhtml/index.html](https://669.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://669.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
