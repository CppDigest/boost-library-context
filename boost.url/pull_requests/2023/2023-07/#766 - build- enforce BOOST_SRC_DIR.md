# #766 build: enforce BOOST_SRC_DIR [Merged]

> Username: alandefreitas  
> Created at: 2023-07-18 18:46:31 UTC  
> Updated at: 2024-02-21 00:54:47 UTC  
> Merged at: 2023-07-18 21:51:35 UTC  
> Closed at: 2023-07-18 21:51:35 UTC  
> Url: https://github.com/boostorg/url/pull/766  

Although c518dbaa already included BOOST_SRC_DIR and removed support for standalone installation, this PR enforces that appropriate BOOST_INCLUDE_LIBRARIES are set according to the build options.  
  
fix #679

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-07-18 19:11:23 UTC  
> Updated_at: 2023-07-18 19:12:59 UTC  
> Url: https://github.com/boostorg/url/pull/766#issuecomment-1640833372  

## [Codecov](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
> Merging [#766](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (73e1aeb) into [develop](https://app.codecov.io/gh/boostorg/url/commit/f06f595ae760b6626764b8a01e3f8197b4016d6f?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) (f06f595) will **not change** coverage.  
> The diff coverage is `100.00%`.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/url/pull/766/graphs/tree.svg?width=650&height=150&src=pr&token=XroexNAcpL&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #766   +/-   ##  
========================================  
  Coverage    97.25%   97.25%             
========================================  
  Files          155      155             
  Lines         8518     8518             
========================================  
  Hits          8284     8284             
  Misses         234      234             
```  
  
  
| [Impacted Files](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [src/parse\_query.cpp](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-c3JjL3BhcnNlX3F1ZXJ5LmNwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f06f595...73e1aeb](https://app.codecov.io/gh/boostorg/url/pull/766?src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2023-07-18 19:13:52 UTC  
> Url: https://github.com/boostorg/url/pull/766#issuecomment-1640841182  

GCOVR code coverage report [https://766.url.prtest2.cppalliance.org/gcovr/index.html](https://766.url.prtest2.cppalliance.org/gcovr/index.html)    
LCOV code coverage report [https://766.url.prtest2.cppalliance.org/genhtml/index.html](https://766.url.prtest2.cppalliance.org/genhtml/index.html)  
Coverage Diff [https://766.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt](https://766.url.prtest2.cppalliance.org/gcovr/coverage_diff.txt)

---
