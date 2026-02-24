# #835 test: cover pct_string_view::decode [Merged]

> Username: alandefreitas  
> Created at: 2024-03-15 19:21:47 UTC  
> Updated at: 2024-03-15 20:56:14 UTC  
> Merged at: 2024-03-15 20:56:14 UTC  
> Closed at: 2024-03-15 20:56:14 UTC  
> Url: https://github.com/boostorg/url/pull/835  

This commit simplifies duplicated code so that we tests all paths for the pct_string_view::decode function. Unreachable paths are also marked.  
  
This is a partial solution to #828, where src/detail/decode.cpp has low coverage.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-15 19:46:32 UTC  
> Url: https://github.com/boostorg/url/pull/835#issuecomment-2000349383  

GCOVR code coverage report [https://835.url.prtest2.cppalliance.org/gcovr/index.html](https://835.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://835.url.prtest2.cppalliance.org/genhtml/index.html](https://835.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://835.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://835.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-15 20:01:58 UTC  
> Updated_at: 2024-03-15 20:23:59 UTC  
> Url: https://github.com/boostorg/url/pull/835#issuecomment-2000368516  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.13%. Comparing base [(`9d4a4a5`)](https://app.codecov.io/gh/boostorg/url/commit/9d4a4a5f15177349c0957ffb271fe0f867027276?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`0ee988f`)](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/835/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #835      +/-   ##  
===========================================  
+ Coverage    99.04%   99.13%   +0.09%       
===========================================  
  Files          157      157                
  Lines         8447     8436      -11       
===========================================  
- Hits          8366     8363       -3       
+ Misses          81       73       -8       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/detail/decode.cpp](https://app.codecov.io/gh/boostorg/url/pull/835?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2RldGFpbC9kZWNvZGUuY3Bw) | `100.00% <100.00%> (+14.03%)` | :arrow_up: |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [9d4a4a5...0ee988f](https://app.codecov.io/gh/boostorg/url/pull/835?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
