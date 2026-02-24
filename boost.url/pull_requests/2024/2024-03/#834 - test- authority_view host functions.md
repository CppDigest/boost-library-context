# #834 test: authority_view host functions [Merged]

> Username: alandefreitas  
> Created at: 2024-03-15 01:15:57 UTC  
> Updated at: 2024-03-15 18:23:26 UTC  
> Merged at: 2024-03-15 18:23:26 UTC  
> Closed at: 2024-03-15 18:23:26 UTC  
> Url: https://github.com/boostorg/url/pull/834  

This commit includes tests for the authority_view host functions when the host type is invalid.  
  
This is a partial solution to #828, where authority_view.cpp has low coverage.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-03-15 01:36:44 UTC  
> Url: https://github.com/boostorg/url/pull/834#issuecomment-1998743176  

GCOVR code coverage report [https://834.url.prtest2.cppalliance.org/gcovr/index.html](https://834.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://834.url.prtest2.cppalliance.org/genhtml/index.html](https://834.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://834.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://834.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-03-15 01:38:51 UTC  
> Updated_at: 2024-03-15 16:55:44 UTC  
> Url: https://github.com/boostorg/url/pull/834#issuecomment-1998746906  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.04%. Comparing base [(`0f97a6d`)](https://app.codecov.io/gh/boostorg/url/commit/0f97a6d4ed135d992ad167dabcbe7b8d4d38a041?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6175a89`)](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/834/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/834?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #834      +/-   ##  
===========================================  
+ Coverage    98.72%   99.04%   +0.31%       
===========================================  
  Files          157      157                
  Lines         8451     8447       -4       
===========================================  
+ Hits          8343     8366      +23       
+ Misses         108       81      -27       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/authority\_view.cpp](https://app.codecov.io/gh/boostorg/url/pull/834?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL2F1dGhvcml0eV92aWV3LmNwcA==) | `100.00% <ø> (+14.88%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/834/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [0f97a6d...6175a89](https://app.codecov.io/gh/boostorg/url/pull/834?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-03-15 16:51:22 UTC  
> Url: https://github.com/boostorg/url/pull/834#issuecomment-2000061858  

GCOVR code coverage report [https://834.url.prtest2.cppalliance.org/gcovr/index.html](https://834.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://834.url.prtest2.cppalliance.org/genhtml/index.html](https://834.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://834.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://834.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
