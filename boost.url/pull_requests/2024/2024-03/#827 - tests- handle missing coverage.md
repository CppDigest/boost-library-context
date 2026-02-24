# #827 tests: handle missing coverage  [Merged]

> Username: alandefreitas  
> Created at: 2024-03-06 20:25:21 UTC  
> Updated at: 2024-03-08 19:03:40 UTC  
> Merged at: 2024-03-08 19:00:30 UTC  
> Closed at: 2024-03-08 19:00:30 UTC  
> Url: https://github.com/boostorg/url/pull/827  

Add many test cases and fixes that cover all missing paths reported in #818.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-06 20:47:07 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1981753375  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-06 21:46:20 UTC  
> Updated_at: 2024-03-08 17:56:18 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1981856318  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.14815%` with `1 lines` in your changes are missing coverage. Please review.  
> Project coverage is 98.27%. Comparing base [(`d9d78ac`)](https://app.codecov.io/gh/boostorg/url/commit/d9d78ac911ddd70a3f8dd35b73941aa4ebbe68c5?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a3d2e06`)](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/827/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #827      +/-   ##  
===========================================  
+ Coverage    97.24%   98.27%   +1.03%       
===========================================  
  Files          157      157                
  Lines         8595     8513      -82       
===========================================  
+ Hits          8358     8366       +8       
+ Misses         237      147      -90       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/params\_encoded\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX2VuY29kZWRfcmVmLmhwcA==) | `100.00% <ø> (ø)` | |  
| [include/boost/url/params\_ref.hpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvcGFyYW1zX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
| [src/detail/normalize.hpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuaHBw) | `100.00% <ø> (ø)` | |  
| [src/params\_encoded\_ref.cpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcmFtc19lbmNvZGVkX3JlZi5jcHA=) | `97.36% <ø> (+26.21%)` | :arrow_up: |  
| [src/params\_ref.cpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcmFtc19yZWYuY3Bw) | `97.26% <ø> (+26.96%)` | :arrow_up: |  
| [src/detail/normalize.cpp](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9ub3JtYWxpemUuY3Bw) | `99.25% <98.14%> (+7.89%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [d9d78ac...a3d2e06](https://app.codecov.io/gh/boostorg/url/pull/827?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-03-06 22:39:59 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1981978351  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 4

> Username: cppalliance-bot  
> Created_at: 2024-03-06 23:31:11 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1982033351  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 5

> Username: cppalliance-bot  
> Created_at: 2024-03-07 20:34:51 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1984377647  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 6

> Username: cppalliance-bot  
> Created_at: 2024-03-08 02:15:13 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1984927065  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 7

> Username: cppalliance-bot  
> Created_at: 2024-03-08 17:40:49 UTC  
> Url: https://github.com/boostorg/url/pull/827#issuecomment-1986132216  

GCOVR code coverage report [https://827.url.prtest2.cppalliance.org/gcovr/index.html](https://827.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://827.url.prtest2.cppalliance.org/genhtml/index.html](https://827.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://827.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
