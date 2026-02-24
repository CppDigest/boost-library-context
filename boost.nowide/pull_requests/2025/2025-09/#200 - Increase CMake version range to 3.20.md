# #200 Increase CMake version range to 3.20 [Merged]

> Username: Flamefire  
> Created at: 2025-09-06 10:59:13 UTC  
> Updated at: 2025-09-07 08:55:23 UTC  
> Merged at: 2025-09-07 08:53:59 UTC  
> Closed at: 2025-09-07 08:53:59 UTC  
> Url: https://github.com/boostorg/nowide/pull/200  

Avoids deprecation warnings with CMake 3.31+  
  
Closes #199

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-06 13:26:37 UTC  
> Url: https://github.com/boostorg/nowide/pull/200#issuecomment-3262161551  

I was pondering if this ought to be distinct for `BOOST_SUPERPROJECT_SOURCE_DIR` mode and stand-alone.  
In super-project mode we agree on something like 3.19 and in stand-alone mode it's a matter for the individual library.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-09-06 14:57:00 UTC  
> Url: https://github.com/boostorg/nowide/pull/200#issuecomment-3262345625  

## [Codecov](https://app.codecov.io/gh/boostorg/nowide/pull/200?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.30%. Comparing base ([`7ba7e46`](https://app.codecov.io/gh/boostorg/nowide/commit/7ba7e46a6a96eab60379a9656792f5b007dc4559?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`feb955d`](https://app.codecov.io/gh/boostorg/nowide/commit/feb955d9c4457e1b398b915d9808c21c978c3e06?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #200      +/-   ##  
===========================================  
+ Coverage    99.10%   99.30%   +0.19%       
===========================================  
  Files           34       34                
  Lines         3591     3591                
===========================================  
+ Hits          3559     3566       +7       
+ Misses          32       25       -7       
```  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/nowide/pull/200/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
