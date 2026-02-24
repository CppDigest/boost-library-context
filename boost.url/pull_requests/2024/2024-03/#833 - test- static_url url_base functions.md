# #833 test: static_url url_base functions [Merged]

> Username: alandefreitas  
> Created at: 2024-03-14 00:12:03 UTC  
> Updated at: 2024-03-14 02:10:05 UTC  
> Merged at: 2024-03-14 02:10:04 UTC  
> Closed at: 2024-03-14 02:10:05 UTC  
> Url: https://github.com/boostorg/url/pull/833  

This commit includes tests for the url_base functions whose behavior is customized by static_url.  
  
This is a partial solution to #828, where static_url.cpp has low coverage.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-03-14 00:34:20 UTC  
> Updated_at: 2024-03-14 00:40:11 UTC  
> Url: https://github.com/boostorg/url/pull/833#issuecomment-1996186661  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 98.72%. Comparing base [(`1a68713`)](https://app.codecov.io/gh/boostorg/url/commit/1a6871339c00a1107fcd8d2161e2edb90787bdd7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`d976e47`)](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/833/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/833?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #833      +/-   ##  
===========================================  
+ Coverage    98.59%   98.72%   +0.12%       
===========================================  
  Files          157      157                
  Lines         8453     8451       -2       
===========================================  
+ Hits          8334     8343       +9       
+ Misses         119      108      -11       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/static\_url.hpp](https://app.codecov.io/gh/boostorg/url/pull/833?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvc3RhdGljX3VybC5ocHA=) | `100.00% <ø> (+18.18%)` | :arrow_up: |  
| [src/static\_url.cpp](https://app.codecov.io/gh/boostorg/url/pull/833?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3N0YXRpY191cmwuY3Bw) | `100.00% <ø> (+29.16%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [1a68713...d976e47](https://app.codecov.io/gh/boostorg/url/pull/833?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2024-03-14 00:36:18 UTC  
> Url: https://github.com/boostorg/url/pull/833#issuecomment-1996188076  

GCOVR code coverage report [https://833.url.prtest2.cppalliance.org/gcovr/index.html](https://833.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://833.url.prtest2.cppalliance.org/genhtml/index.html](https://833.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://833.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://833.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
