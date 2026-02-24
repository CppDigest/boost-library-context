# #687 Adjust CircleCI setup to account for Boost.Inspect build change. [Closed]

> Username: grafikrobot  
> Created at: 2025-05-06 04:09:02 UTC  
> Updated at: 2025-07-03 03:52:37 UTC  
> Closed at: 2025-07-03 02:56:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687  

In addition to changing how to build the inspect tool this change Also simplifies the build and test to avoid future config problems as it can more easily adjust to future CI changes. It bumps the docker image to what the current stable image is to auto-adjust to recent stable gcc compiler.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-05-06 05:35:53 UTC  
> Updated_at: 2025-07-03 03:52:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-2853331977  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/687?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 94.7%. Comparing base [(`f81c13b`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/f81c13bb2e32097628e60680303fd75a69799c74?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`a13af34`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/a13af34a922d09bd481b8fcbcf86a4c5d98f79c7?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/687/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/687?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff           @@  
##           develop    #687   +/-   ##  
=======================================  
  Coverage     94.7%   94.7%             
=======================================  
  Files          296     296             
  Lines        30881   30881             
=======================================  
  Hits         29233   29233             
  Misses        1648    1648             
```  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/687?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/687?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [f81c13b...a13af34](https://app.codecov.io/gh/boostorg/multiprecision/pull/687?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-05-06 08:16:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-2853665166  

Quickbook builds fine for me with gcc-13, so I'm not sure why it would be doing that.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2025-05-06 10:09:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-2853996727  

This fix to Config gets it: https://github.com/boostorg/config/pull/517  
  
Note that the point of the original comment, was that moving the dist-bin target to a different directory is a breaking change that breaks several libraries CI builds.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2025-05-09 01:18:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-2864828727  

@jzmaddock can you rerun the circleci test? As it doesn't rerun on close/reopen.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-05-09 08:20:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-2865616443  

@grafikrobot : still seems to fail with the strange internal compiler errors :(

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2025-07-01 21:28:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/687#issuecomment-3025591894  

John got this thing working on develop.  
  
Is this issue still relevant? Please don't tell me I have to overlook RED for another cycle.  
  
@jzmaddock and @grafikrobot and @mborland

---
