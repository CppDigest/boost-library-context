# #1402 Example/CppCon2018, Include error_code to acceptor_.set_option() call [Closed]

> Username: johan-ronnkvist  
> Created at: 2019-01-05 13:07:16 UTC  
> Updated at: 2019-01-22 00:12:50 UTC  
> Closed at: 2019-01-22 00:12:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1402  

Looking at the example code, it seems like this should be included in the call in order to update the content of the error_code variable.  
  
If that's not the intention, the test for the error_code on the line after is likely unnecessary.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2019-01-05 14:39:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1402#issuecomment-451661035  

Yep! Might need that here too:  
https://github.com/vinniefalco/CppCon2018

---

## Comment 2

> Username: johan-ronnkvist  
> Created_at: 2019-01-05 14:42:36 UTC  
> Url: https://github.com/boostorg/beast/pull/1402#issuecomment-451661297  

I'll add another PR for that repo too.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2019-01-05 15:28:37 UTC  
> Url: https://github.com/boostorg/beast/pull/1402#issuecomment-451664732  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=h1) Report  
> Merging [#1402](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/8b72b4d407674176187f7427bd1e6a7c88192119?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1402/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1402   +/-   ##  
========================================  
  Coverage    92.93%   92.93%             
========================================  
  Files          118      118             
  Lines        11366    11366             
========================================  
  Hits         10563    10563             
  Misses         803      803  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=footer). Last update [8b72b4d...066af76](https://codecov.io/gh/boostorg/beast/pull/1402?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-01-22 00:12:50 UTC  
> Url: https://github.com/boostorg/beast/pull/1402#issuecomment-456229796  

This was merged to 203 https://github.com/boostorg/beast/commit/be800436ea40c2d0633e3c1f82583b94e45f6d1f Thanks!

---
