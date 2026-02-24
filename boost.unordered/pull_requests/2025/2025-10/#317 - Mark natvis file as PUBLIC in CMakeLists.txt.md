# #317 Mark natvis file as PUBLIC in CMakeLists.txt [Merged]

> Username: k3DW  
> Created at: 2025-10-05 20:09:13 UTC  
> Updated at: 2026-01-01 20:02:34 UTC  
> Merged at: 2026-01-01 20:00:48 UTC  
> Closed at: 2026-01-01 20:00:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/317  

Closes #307 , in tandem with boostorg/cmake#89  
  
This change mirror's the [CMake code in Boost.Assert](https://github.com/boostorg/assert/blob/e107bd7b556d72ef5273c17f4099198a28edaa4d/CMakeLists.txt#L20-L43)

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-05 23:43:51 UTC  
> Updated_at: 2025-10-19 11:10:26 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#issuecomment-3369521937  

## [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/317?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 98.23%. Comparing base ([`e01b7b5`](https://app.codecov.io/gh/boostorg/unordered/commit/e01b7b52a6ac1ea462aa470a52cb08a879903ff7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`37bd215`](https://app.codecov.io/gh/boostorg/unordered/commit/37bd2157772032307c6d866d1f95ee16a4fef255?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/unordered/pull/317/graphs/tree.svg?width=650&height=150&src=pr&token=ZqRPZlJZ5N&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/unordered/pull/317?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #317   +/-   ##  
========================================  
  Coverage    98.23%   98.23%             
========================================  
  Files          161      161             
  Lines        22239    22239             
========================================  
  Hits         21847    21847             
  Misses         392      392             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/unordered/pull/317?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/unordered/pull/317?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [e01b7b5...37bd215](https://app.codecov.io/gh/boostorg/unordered/pull/317?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2025-10-19 07:31:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/317#pullrequestreview-3353989512  

📁 CMakeLists.txt

```diff
  26 |- if(CMAKE_VERSION VERSION_GREATER 3.18 AND CMAKE_GENERATOR MATCHES "Visual Studio")
  27 |- 
  26 |+ if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.12)
```

> Username: pdimov  
> Created_at: 2025-10-19 07:31:13 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#discussion_r2442804383  

You don't need this check.

> Username: pdimov  
> Created_at: 2025-10-19 07:37:38 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#discussion_r2442814058  

I mean, CONFIGURE_DEPENDS "works" on pre-3.12, because it's interpreted as a path, and doesn't do anything.


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-10-19 07:33:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/317#pullrequestreview-3353990602  

📁 CMakeLists.txt

```diff
  30 |+ endif()
  31 |+ target_sources(boost_unordered PRIVATE ${boost_unordered_IDEFILES})
  32 |+ if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.18)
```

> Username: pdimov  
> Created_at: 2025-10-19 07:33:03 UTC  
> Updated_at: 2025-10-19 07:33:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#discussion_r2442805340  

This is wrong. `target_sources` requires 3.19, not `source_group` and not 3.18.


---

## Comment 4

> Username: pdimov  
> Created_at: 2025-10-20 10:29:52 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#issuecomment-3421491075  

This is what I arrived at, after a fair bit of experimentation:  
  
https://github.com/boostorg/assert/blob/e107bd7b556d72ef5273c17f4099198a28edaa4d/CMakeLists.txt#L20-L43

---

## Comment 5

> Username: k3DW  
> Created_at: 2025-10-22 02:03:43 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#issuecomment-3430195673  

I will look into this more soon, thanks

---

## Comment 6

> Username: k3DW  
> Created_at: 2025-12-31 23:47:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/317#issuecomment-3703059834  

> This is what I arrived at, after a fair bit of experimentation:  
>   
> https://github.com/boostorg/assert/blob/e107bd7b556d72ef5273c17f4099198a28edaa4d/CMakeLists.txt#L20-L43  
  
Alright, sorry for taking so long. This solution looks great, I have nothing to change. I'm updating the PR to mirror Boost.Assert's CMake code.

---
