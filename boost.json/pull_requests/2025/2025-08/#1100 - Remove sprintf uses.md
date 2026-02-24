# #1100 Remove sprintf uses [Merged]

> Username: grisumbras  
> Created at: 2025-08-21 08:33:22 UTC  
> Updated at: 2025-08-22 16:56:39 UTC  
> Merged at: 2025-08-22 10:08:55 UTC  
> Closed at: 2025-08-22 10:08:55 UTC  
> Url: https://github.com/boostorg/json/pull/1100  



---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-08-21 08:40:16 UTC  
> Url: https://github.com/boostorg/json/pull/1100#issuecomment-3209571433  

An automated preview of the documentation is available at [https://1100.json.prtest2.cppalliance.org/libs/json/doc/html/index.html](https://1100.json.prtest2.cppalliance.org/libs/json/doc/html/index.html)

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-08-21 09:26:49 UTC  
> Updated_at: 2025-08-22 16:56:39 UTC  
> Url: https://github.com/boostorg/json/pull/1100#issuecomment-3209728635  

## [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1100?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 93.71%. Comparing base ([`e9daad5`](https://app.codecov.io/gh/boostorg/json/commit/e9daad5d0e8b819817c9b217a25b279be3195ff9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2df2366`](https://app.codecov.io/gh/boostorg/json/commit/2df2366f74ad0ef4162855fb38dde155dbf42453?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/json/pull/1100/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/json/pull/1100?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1100   +/-   ##  
========================================  
  Coverage    93.71%   93.71%             
========================================  
  Files           91       91             
  Lines         9156     9156             
========================================  
  Hits          8581     8581             
  Misses         575      575             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/json/pull/1100?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/json/pull/1100?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e9daad5...2df2366](https://app.codecov.io/gh/boostorg/json/pull/1100?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2025-08-21 09:41:59 UTC  
> Url: https://github.com/boostorg/json/pull/1100#issuecomment-3209776240  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1100/pullrequest-condensed-9eba52d.png)  
 Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1100/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/1100/pullrequest.html)

---

## Comment 4

> Username: sdarwin  
> Created_at: 2025-08-21 12:33:43 UTC  
> Url: https://github.com/boostorg/json/pull/1100#issuecomment-3210418797  

@grisumbras   
  
You are solving the issue by "removing sprintf".   A google search shows sprintf is deprecated on macos. See attached file for a list of boost libraries that include it. Would you agree, or not: I should open github issues on those boost libraries. Is it reasonable to have "sprintf" in the code?  
  
[sprintf.txt](https://github.com/user-attachments/files/21917730/sprintf.txt)

---

## Comment 5

> Username: grisumbras  
> Created_at: 2025-08-21 12:55:01 UTC  
> Url: https://github.com/boostorg/json/pull/1100#issuecomment-3210487385  

Yes, `sprintf` is "unsafe" and it is recommended to replace it with `snprintf`. The issue is that it might not be available on all the platforms a library supports.

---
