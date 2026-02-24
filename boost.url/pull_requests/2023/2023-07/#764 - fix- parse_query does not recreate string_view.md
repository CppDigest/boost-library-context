# #764 fix: parse_query does not recreate string_view [Merged]

> Username: alandefreitas  
> Created at: 2023-07-18 17:08:33 UTC  
> Updated at: 2023-07-24 14:37:31 UTC  
> Merged at: 2023-07-18 18:34:23 UTC  
> Closed at: 2023-07-18 18:34:23 UTC  
> Url: https://github.com/boostorg/url/pull/764  

fix #757

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-18 17:31:09 UTC  
> Updated_at: 2023-07-18 17:33:44 UTC  
> Url: https://github.com/boostorg/url/pull/764#issuecomment-1640665256  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#764](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (9a1f5a7) into [develop](https://app.codecov.io/gh/boostorg/url/commit/f06f595ae760b6626764b8a01e3f8197b4016d6f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f06f595) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/764/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #764   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8518     8518             
========================================  
  Hits          8284     8284             
  Misses         234      234             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/parse\_query.cpp](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcnNlX3F1ZXJ5LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f06f595...9a1f5a7](https://app.codecov.io/gh/boostorg/url/pull/764?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-07-18 17:33:10 UTC  
> Url: https://github.com/boostorg/url/pull/764#issuecomment-1640667730  

GCOVR code coverage report [https://764.url.prtest2.cppalliance.org/gcovr/index.html](https://764.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://764.url.prtest2.cppalliance.org/genhtml/index.html](https://764.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://764.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://764.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
