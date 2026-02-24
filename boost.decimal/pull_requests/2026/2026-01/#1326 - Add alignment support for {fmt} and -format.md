# #1326 Add alignment support for {fmt} and <format> [Merged]

> Username: mborland  
> Created at: 2026-01-28 19:17:35 UTC  
> Updated at: 2026-01-28 22:43:36 UTC  
> Merged at: 2026-01-28 21:57:40 UTC  
> Closed at: 2026-01-28 21:57:41 UTC  
> Url: https://github.com/boostorg/decimal/pull/1326  

Closes: #1167   
Closes: #1168   
Closes: #1169

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2026-01-28 19:24:17 UTC  
> Url: https://github.com/boostorg/decimal/pull/1326#issuecomment-3813404839  

An automated preview of the documentation is available at [https://1326.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html](https://1326.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2026-01-28 19:24:16 UTC

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2026-01-28 22:32:58 UTC  
> Updated_at: 2026-01-28 22:43:36 UTC  
> Url: https://github.com/boostorg/decimal/pull/1326#issuecomment-3814272640  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.8%. Comparing base ([`3529ce1`](https://app.codecov.io/gh/cppalliance/decimal/commit/3529ce112489a3cf71dedd3fab56a0a8ef745239?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`1ea8ae7`](https://app.codecov.io/gh/cppalliance/decimal/commit/1ea8ae7357fd7f66caec2b7e864152bd068d8a3d?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 5 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1326/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@            Coverage Diff            @@  
##           develop   #1326     +/-   ##  
=========================================  
- Coverage     98.8%   98.8%   -0.0%       
=========================================  
  Files          277     277               
  Lines        18012   18008      -4       
  Branches      1915    1915               
=========================================  
- Hits         17791   17785      -6       
- Misses         221     223      +2       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) | Coverage Δ | |  
|---|---|---|  
| [test/test\_format.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?src=pr&el=tree&filepath=test%2Ftest_format.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zvcm1hdC5jcHA=) | `100.0% <ø> (ø)` | |  
| [test/test\_format\_fmtlib.cpp](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?src=pr&el=tree&filepath=test%2Ftest_format_fmtlib.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance#diff-dGVzdC90ZXN0X2Zvcm1hdF9mbXRsaWIuY3Bw) | `100.0% <ø> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/cppalliance/decimal/pull/1326/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [3529ce1...1ea8ae7](https://app.codecov.io/gh/cppalliance/decimal/pull/1326?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
