# #765 fix: url_view string_view constructor requires non-url_view_base [Merged]

> Username: alandefreitas  
> Created at: 2023-07-18 18:35:45 UTC  
> Updated at: 2023-07-24 14:37:29 UTC  
> Merged at: 2023-07-18 21:52:18 UTC  
> Closed at: 2023-07-18 21:52:18 UTC  
> Url: https://github.com/boostorg/url/pull/765  

fix #756

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-07-18 18:56:26 UTC  
> Url: https://github.com/boostorg/url/pull/765#issuecomment-1640792170  

GCOVR code coverage report [https://765.url.prtest2.cppalliance.org/gcovr/index.html](https://765.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://765.url.prtest2.cppalliance.org/genhtml/index.html](https://765.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://765.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://765.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-07-18 18:59:43 UTC  
> Updated_at: 2023-07-18 19:03:45 UTC  
> Url: https://github.com/boostorg/url/pull/765#issuecomment-1640803114  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#765](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1d6b369) into [develop](https://app.codecov.io/gh/boostorg/url/commit/c97bc2782cdd9b343ede1492863a672805c255cd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (c97bc27) will **increase** coverage by `0.01%`.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/765/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #765      +/-   ##  
===========================================  
+ Coverage    97.25%   97.26%   +0.01%       
===========================================  
  Files          155      155                
  Lines         8518     8518                
===========================================  
+ Hits          8284     8285       +1       
+ Misses         234      233       -1       
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/url/url\_view.hpp](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC91cmwvdXJsX3ZpZXcuaHBw) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/url/pull/765/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [c97bc27...1d6b369](https://app.codecov.io/gh/boostorg/url/pull/765?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
