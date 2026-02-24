# #1272 Fix CI deprecations [Closed]

> Username: mborland  
> Created at: 2025-06-10 17:39:25 UTC  
> Updated at: 2025-06-11 10:32:27 UTC  
> Closed at: 2025-06-10 20:16:35 UTC  
> Url: https://github.com/boostorg/math/pull/1272  

Windows-2019 and Ubuntu-22.04 are deprecated in Github Actions, pending removal at the end of the month. Move runs that we can to Drone.

---

## Comment 1

> Username: mborland  
> Created_at: 2025-06-10 18:28:12 UTC  
> Url: https://github.com/boostorg/math/pull/1272#issuecomment-2960233981  

@jzmaddock Have you had any issues with GCC 12 locally? It's strange that Windows GCC is failing the thread sanity check test after updating from 2019 to 2022 OS versions.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-10 19:25:51 UTC  
> Updated_at: 2025-06-10 20:17:04 UTC  
> Url: https://github.com/boostorg/math/pull/1272#issuecomment-2960363523  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1272?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.07%. Comparing base [(`2b05d87`)](https://app.codecov.io/gh/boostorg/math/commit/2b05d87c0f62d873bb160873ac130e3dc4428079?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`6c8f87f`)](https://app.codecov.io/gh/boostorg/math/commit/6c8f87f408f2d032a06ee86e7b3b2c7c15f5c0c9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1272/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1272?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1272   +/-   ##  
========================================  
  Coverage    95.07%   95.07%             
========================================  
  Files          792      792             
  Lines        66882    66882             
========================================  
  Hits         63589    63589             
  Misses        3293     3293             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1272?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1272?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2b05d87...6c8f87f](https://app.codecov.io/gh/boostorg/math/pull/1272?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: mborland  
> Created_at: 2025-06-10 20:03:06 UTC  
> Url: https://github.com/boostorg/math/pull/1272#issuecomment-2960446413  

Yeah GCC 12 and 14 both seem to be showing the MinGW thread_local destruction problem that I thought had been fixed

---

## Comment 4

> Username: mborland  
> Created_at: 2025-06-10 20:16:47 UTC  
> Url: https://github.com/boostorg/math/pull/1272#issuecomment-2960477462  

Rolled into #1271

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-06-11 10:32:27 UTC  
> Url: https://github.com/boostorg/math/pull/1272#issuecomment-2962143034  

> Have you had any issues with GCC 12 locally? It's strange that Windows GCC is failing the thread sanity check test after updating from 2019 to 2022 OS versions.  
  
Fails for me with cygwin gcc-11, passes with mingw gcc-13 and 14 (I don't have 12 installed at present).  
  
But.... there are multiple mingw builds with different object and threading models :(

---
