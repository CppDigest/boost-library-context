# #778 Fix Windows build of file_router example [Merged]

> Username: ecatmur  
> Created at: 2023-08-14 20:08:44 UTC  
> Updated at: 2023-08-15 16:33:55 UTC  
> Merged at: 2023-08-15 16:33:55 UTC  
> Closed at: 2023-08-15 16:33:55 UTC  
> Url: https://github.com/boostorg/url/pull/778  

On Windows, fs::path::c_str() returns wchar_t const* so cannot be streamed to std::cout. fs::path is output streamable anyway (it might quote it, but that should be fine).

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-08-14 20:31:13 UTC  
> Updated_at: 2023-08-14 20:34:40 UTC  
> Url: https://github.com/boostorg/url/pull/778#issuecomment-1678016651  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#778](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (052e58b) into [develop](https://app.codecov.io/gh/boostorg/url/commit/a1181275d02a0a4c6ab8147354f752ec36e1dd98?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (a118127) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/778/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #778   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a118127...052e58b](https://app.codecov.io/gh/boostorg/url/pull/778?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-08-14 20:34:06 UTC  
> Url: https://github.com/boostorg/url/pull/778#issuecomment-1678020214  

GCOVR code coverage report [https://778.url.prtest2.cppalliance.org/gcovr/index.html](https://778.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://778.url.prtest2.cppalliance.org/genhtml/index.html](https://778.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://778.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://778.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2023-08-15 16:32:55 UTC  
> Url: https://github.com/boostorg/url/pull/778#issuecomment-1679262374  

Looks good. Thanks! 😀

---
