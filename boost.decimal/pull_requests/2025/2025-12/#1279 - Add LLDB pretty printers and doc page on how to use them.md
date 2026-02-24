# #1279 Add LLDB pretty printers and doc page on how to use them [Merged]

> Username: mborland  
> Created at: 2025-12-10 15:49:53 UTC  
> Updated at: 2025-12-10 19:22:11 UTC  
> Merged at: 2025-12-10 19:21:50 UTC  
> Closed at: 2025-12-10 19:21:50 UTC  
> Url: https://github.com/boostorg/decimal/pull/1279  

The python files are written to be modular so that we can use the decoding functions to build out support for GDB  
  
Closes: #1278

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-12-10 15:56:23 UTC  
> Url: https://github.com/boostorg/decimal/pull/1279#issuecomment-3637775852  

An automated preview of the documentation is available at [https://1279.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html](https://1279.decimal.prtest3.cppalliance.org/libs/decimal/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-12-10 15:56:22 UTC

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-12-10 18:44:06 UTC  
> Updated_at: 2025-12-10 19:22:11 UTC  
> Url: https://github.com/boostorg/decimal/pull/1279#issuecomment-3638472588  

## [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1279?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.0%. Comparing base ([`07a7cdf`](https://app.codecov.io/gh/cppalliance/decimal/commit/07a7cdfa503928e9cb0ff56afa7caea95cc18dbf?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)) to head ([`a7b4eb4`](https://app.codecov.io/gh/cppalliance/decimal/commit/a7b4eb4b8ea37ace5a06eeecec8757f407cbce85?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)).  
:warning: Report is 33 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/cppalliance/decimal/pull/1279/graphs/tree.svg?width=650&height=150&src=pr&token=drvY8nnV5S&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)](https://app.codecov.io/gh/cppalliance/decimal/pull/1279?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
  
```diff  
@@           Coverage Diff           @@  
##           develop   #1279   +/-   ##  
=======================================  
  Coverage     99.0%   99.0%             
=======================================  
  Files          268     268             
  Lines        17746   17746             
  Branches      1980    1980             
=======================================  
  Hits         17553   17553             
  Misses         193     193             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/cppalliance/decimal/pull/1279?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/cppalliance/decimal/pull/1279?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Last update [07a7cdf...a7b4eb4](https://app.codecov.io/gh/cppalliance/decimal/pull/1279?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=cppalliance).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
