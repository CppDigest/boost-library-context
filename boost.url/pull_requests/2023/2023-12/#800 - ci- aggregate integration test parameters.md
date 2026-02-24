# #800 ci: aggregate integration test parameters [Merged]

> Username: alandefreitas  
> Created at: 2023-12-09 10:53:41 UTC  
> Updated at: 2024-02-21 00:55:04 UTC  
> Merged at: 2023-12-11 16:39:05 UTC  
> Closed at: 2023-12-11 16:39:05 UTC  
> Url: https://github.com/boostorg/url/pull/800  

This PR simplifies the test steps by aggregating common parameters for the cmake workflows. To achieve that, all cmake related actions have been ported to JavaScript, which has better support, provides more features, and is easier to maintain. A lot of bugs were fixed in the process.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2023-12-09 11:14:40 UTC  
> Url: https://github.com/boostorg/url/pull/800#issuecomment-1848377768  

GCOVR code coverage report [https://800.url.prtest2.cppalliance.org/gcovr/index.html](https://800.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://800.url.prtest2.cppalliance.org/genhtml/index.html](https://800.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://800.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://800.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2023-12-09 11:16:05 UTC  
> Updated_at: 2023-12-09 11:18:53 UTC  
> Url: https://github.com/boostorg/url/pull/800#issuecomment-1848377997  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#800](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (7d0558d) into [develop](https://app.codecov.io/gh/boostorg/url/commit/f42fd62ecf26ba3195493524020739f4615e14a8?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f42fd62) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/800/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #800   +/-   ##  
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
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f42fd62...7d0558d](https://app.codecov.io/gh/boostorg/url/pull/800?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---
