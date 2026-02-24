# #103 Build examples in CMake [Closed]

> Username: LegalizeAdulthood  
> Created at: 2025-09-30 23:08:47 UTC  
> Updated at: 2025-12-12 06:32:40 UTC  
> Closed at: 2025-12-12 06:32:28 UTC  
> Url: https://github.com/boostorg/odeint/pull/103  

This adds CMake build script support for building the examples that are currently built with bjam.  
  
This should help in preventing build-rot from creeping into the example programs.  
  
I ran all the examples manually to verify that they didn't assert or crash.  
  
I'm open to discussion on the changes made to the top-level CMakeLists.txt to find Boost dependencies;  
this was necessary for me to build the examples by checking out this repository alone.  
  
Please let me know if you prefer to have everything squished to a single commit; that's not a problem for me.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-01 14:49:44 UTC  
> Updated_at: 2025-12-12 06:32:40 UTC  
> Url: https://github.com/boostorg/odeint/pull/103#issuecomment-3356801191  

## [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/103?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 99.40%. Comparing base ([`91a4a91`](https://app.codecov.io/gh/boostorg/odeint/commit/91a4a916fa193b02bda2ffc3920953387acae962?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`54108fb`](https://app.codecov.io/gh/boostorg/odeint/commit/54108fb909dc3747692f35e3b6b343ac6531623b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 17 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/odeint/pull/103/graphs/tree.svg?width=650&height=150&src=pr&token=Er6mj5aLOG&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/odeint/pull/103?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #103      +/-   ##  
===========================================  
- Coverage    99.43%   99.40%   -0.03%       
===========================================  
  Files          168      168                
  Lines         7556     7556                
===========================================  
- Hits          7513     7511       -2       
- Misses          43       45       +2       
```  
[see 1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/odeint/pull/103/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/odeint/pull/103?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/odeint/pull/103?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [91a4a91...54108fb](https://app.codecov.io/gh/boostorg/odeint/pull/103?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mborland  
> Created_at: 2025-12-01 10:14:24 UTC  
> Url: https://github.com/boostorg/odeint/pull/103#issuecomment-3595715797  

I think this can be made much simpler. We already have `boost_test_jamfile` which allows us to run all the tests in a jam file via cmake with a trivial amount of code. See example here: https://github.com/boostorg/charconv/blob/develop/test/CMakeLists.txt.  
  
The examples could be added to run in the test/ Jamfile (as they are in many libraries) and then `boost_test_jamfile` runs on everything in test/ so we can do both tests and examples via CMake.

---

## Comment 3

> Username: LegalizeAdulthood  
> Created_at: 2025-12-01 16:25:22 UTC  
> Url: https://github.com/boostorg/odeint/pull/103#issuecomment-3597503870  

That sounds fine to me, feel free to discard this if there is some other magical way of making it happen in bjam and CMake.  Boost.build is nebulous to me, although I understand CMake pretty well.

---

## Comment 4

> Username: LegalizeAdulthood  
> Created_at: 2025-12-12 03:34:47 UTC  
> Url: https://github.com/boostorg/odeint/pull/103#issuecomment-3644763634  

Should we close this PR then?

---
