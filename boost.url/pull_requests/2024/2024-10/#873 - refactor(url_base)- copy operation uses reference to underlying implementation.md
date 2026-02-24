# #873 refactor(url_base): copy operation uses reference to underlying implementation [Merged]

> Username: alandefreitas  
> Created at: 2024-10-24 19:22:02 UTC  
> Updated at: 2024-10-25 01:02:32 UTC  
> Merged at: 2024-10-25 01:01:22 UTC  
> Closed at: 2024-10-25 01:01:22 UTC  
> Url: https://github.com/boostorg/url/pull/873  

The copy operation from a url_view_base should use the pointed implementation rather than the underlying implementation so that url_views that refer to other urls are accounted for.  
  
fix #872

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-24 19:23:40 UTC  
> Url: https://github.com/boostorg/url/pull/873#issuecomment-2436174818  

Antora version: an automated preview of the documentation is available at [https://873.urlantora.prtest2.cppalliance.org/site/index.html](https://873.urlantora.prtest2.cppalliance.org/site/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2024-10-24 19:47:08 UTC  
> Updated_at: 2024-10-25 01:02:32 UTC  
> Url: https://github.com/boostorg/url/pull/873#issuecomment-2436213867  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/873?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 99.20%. Comparing base [(`2e58683`)](https://app.codecov.io/gh/boostorg/url/commit/2e5868300f4902d4d6dcb0c7d820c7dc250bdb15?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dddea38`)](https://app.codecov.io/gh/boostorg/url/commit/dddea381dc3bc7412e214c026e8d86ba1e4160fc?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/873/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/873?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #873      +/-   ##  
===========================================  
+ Coverage    99.19%   99.20%   +0.01%       
===========================================  
  Files          157      157                
  Lines         8415     8415                
===========================================  
+ Hits          8347     8348       +1       
+ Misses          68       67       -1       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/url/pull/873?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_base.hpp](https://app.codecov.io/gh/boostorg/url/pull/873?src=pr&el=tree&filepath=include%2Fboost%2Furl%2Furl_base.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX2Jhc2UuaHBw) | `100.00% <ø> (ø)` | |  
| [src/url\_base.cpp](https://app.codecov.io/gh/boostorg/url/pull/873?src=pr&el=tree&filepath=src%2Furl_base.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3VybF9iYXNlLmNwcA==) | `99.63% <100.00%> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/873/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/873?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/873?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2e58683...dddea38](https://app.codecov.io/gh/boostorg/url/pull/873?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-10-24 19:51:48 UTC  
> Url: https://github.com/boostorg/url/pull/873#issuecomment-2436221078  

GCOVR code coverage report [https://873.url.prtest2.cppalliance.org/gcovr/index.html](https://873.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://873.url.prtest2.cppalliance.org/genhtml/index.html](https://873.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://873.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://873.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
