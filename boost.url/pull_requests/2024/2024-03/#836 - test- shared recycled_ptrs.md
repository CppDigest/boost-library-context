# #836 test: shared recycled_ptrs [Merged]

> Username: alandefreitas  
> Created at: 2024-03-15 21:13:28 UTC  
> Updated at: 2024-03-16 00:01:16 UTC  
> Merged at: 2024-03-16 00:01:16 UTC  
> Closed at: 2024-03-16 00:01:16 UTC  
> Url: https://github.com/boostorg/url/pull/836  

This commit includes tests for shared `recycled_ptr`s. Unreachable paths are also marked.  
  
This is the last in a series of commits that intend to fix #828, where `recycled_ptr` had low coverage.  
  
fix #828

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-15 21:36:17 UTC  
> Url: https://github.com/boostorg/url/pull/836#issuecomment-2000477729  

GCOVR code coverage report [https://836.url.prtest2.cppalliance.org/gcovr/index.html](https://836.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://836.url.prtest2.cppalliance.org/genhtml/index.html](https://836.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://836.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://836.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-15 21:37:56 UTC  
> Updated_at: 2024-03-15 21:40:50 UTC  
> Url: https://github.com/boostorg/url/pull/836#issuecomment-2000479339  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.21%. Comparing base [(`5b86565`)](https://app.codecov.io/gh/boostorg/url/commit/5b86565807bdd08b293fb939862fa68a5b92bade?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`f7bd5a6`)](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/836/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #836      +/-   ##  
===========================================  
+ Coverage    99.13%   99.21%   +0.08%       
===========================================  
  Files          157      157                
  Lines         8436     8422      -14       
===========================================  
- Hits          8363     8356       -7       
+ Misses          73       66       -7       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/grammar/detail/recycled.cpp](https://app.codecov.io/gh/boostorg/url/pull/836?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2dyYW1tYXIvZGV0YWlsL3JlY3ljbGVkLmNwcA==) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/836/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [5b86565...f7bd5a6](https://app.codecov.io/gh/boostorg/url/pull/836?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
