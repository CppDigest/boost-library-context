# #803 fix: handle brackets in query_chars [Merged]

> Username: alandefreitas  
> Created at: 2023-12-15 15:17:36 UTC  
> Updated at: 2024-02-21 00:55:31 UTC  
> Merged at: 2023-12-15 18:42:28 UTC  
> Closed at: 2023-12-15 18:42:28 UTC  
> Url: https://github.com/boostorg/url/pull/803  

Although 1317ca8c includes support for brackets in key_chars, the lack of support in query_chars made still led to errors when the the complete url is parsed at once.  
  
fix #93

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-12-15 15:40:24 UTC  
> Url: https://github.com/boostorg/url/pull/803#issuecomment-1858079595  

GCOVR code coverage report [https://803.url.prtest2.cppalliance.org/gcovr/index.html](https://803.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://803.url.prtest2.cppalliance.org/genhtml/index.html](https://803.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://803.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://803.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-12-15 15:52:44 UTC  
> Url: https://github.com/boostorg/url/pull/803#issuecomment-1858098388  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#803](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (1696fd6) into [develop](https://app.codecov.io/gh/boostorg/url/commit/02c6984a018770d0d40fff62055c29c444ff37dd?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (02c6984) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/803/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #803   +/-   ##  
========================================  
  Coverage    97.27%   97.27%             
========================================  
  Files          156      156             
  Lines         8565     8565             
========================================  
  Hits          8332     8332             
  Misses         233      233             
```  
  
  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [02c6984...1696fd6](https://app.codecov.io/gh/boostorg/url/pull/803?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
