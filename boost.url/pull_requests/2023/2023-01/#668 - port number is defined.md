# #668 port number is defined [Merged]

> Username: alandefreitas  
> Created at: 2023-01-25 00:03:25 UTC  
> Updated at: 2024-02-21 00:56:11 UTC  
> Merged at: 2023-01-25 13:24:48 UTC  
> Closed at: 2023-01-25 13:24:48 UTC  
> Url: https://github.com/boostorg/url/pull/668  

fix #666

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-01-25 00:04:05 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402851675  

"port number does not overflow"

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2023-01-25 00:14:42 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402863662  

> "port number does not overflow"  
  
But it does :)  
... then stores the string and sets the number to zero.

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2023-01-25 00:18:28 UTC  
> Updated_at: 2023-01-25 12:51:22 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402867240  

# [Codecov](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#668](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (cb14fbc) into [develop](https://codecov.io/gh/boostorg/url/commit/f7c870ef0056802bafd0ddd12a525c833641b5eb?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f7c870e) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/url/pull/668/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #668   +/-   ##  
========================================  
  Coverage    97.13%   97.13%             
========================================  
  Files          152      152             
  Lines         8435     8441    +6       
========================================  
+ Hits          8193     8199    +6       
  Misses         242      242             
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/rfc/detail/impl/port\_rule.ipp](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcmZjL2RldGFpbC9pbXBsL3BvcnRfcnVsZS5pcHA=) | `97.77% <100.00%> (+0.34%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f7c870e...cb14fbc](https://codecov.io/gh/boostorg/url/pull/668?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2023-01-25 00:20:42 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402869349  

GCOVR code coverage report [https://668.url.prtest.cppalliance.org/gcovr/index.html](https://668.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://668.url.prtest.cppalliance.org/genhtml/index.html](https://668.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2023-01-25 00:21:19 UTC  
> Updated_at: 2023-01-25 00:21:34 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402869870  

"port number is defined (#666)"

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2023-01-25 01:10:38 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1402909514  

GCOVR code coverage report [https://668.url.prtest.cppalliance.org/gcovr/index.html](https://668.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://668.url.prtest.cppalliance.org/genhtml/index.html](https://668.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2023-01-25 12:45:12 UTC  
> Url: https://github.com/boostorg/url/pull/668#issuecomment-1403552389  

GCOVR code coverage report [https://668.url.prtest.cppalliance.org/gcovr/index.html](https://668.url.prtest.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://668.url.prtest.cppalliance.org/genhtml/index.html](https://668.url.prtest.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt](https://668.url.prtest.cppalliance.org/gcovr/coverage_diff.txt)

---
