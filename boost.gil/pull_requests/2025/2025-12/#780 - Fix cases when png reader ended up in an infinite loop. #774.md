# #780 Fix cases when png reader ended up in an infinite loop. #774 [Merged]

> Username: Processor228  
> Created at: 2025-12-03 19:28:29 UTC  
> Updated at: 2025-12-08 14:18:44 UTC  
> Merged at: 2025-12-05 19:45:19 UTC  
> Closed at: 2025-12-05 19:45:19 UTC  
> Url: https://github.com/boostorg/gil/pull/780  

Additionally, throw exceptions directly, instead of handling errors by longjumps.  
  
Longjumps lead to segfaults under compiler optimizations.  
  
Besides, setjmp setups always ended up throwing  
exceptions, so why not throwing them right away instead?  
  
### Description  
  
A fix for issue #774, case when png reader ends up in infinite loop.  
  
### References  
  
issue #774   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2025-12-03 19:56:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/780#pullrequestreview-3536690311  

Although I'm not libpng expert, I'm inclined to approve it. The `setjmp` always been itching me too :) Thanks!  
If not objections arrive in couple of days, this will be merged.

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-12-03 20:00:21 UTC  
> Updated_at: 2025-12-08 14:18:44 UTC  
> Url: https://github.com/boostorg/gil/pull/780#issuecomment-3608608868  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/780?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `25.00000%` with `6 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 81.97%. Comparing base ([`06c421a`](https://app.codecov.io/gh/boostorg/gil/commit/06c421ae74062bfa112dc3bacaef7746a85467d2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`6281653`](https://app.codecov.io/gh/boostorg/gil/commit/628165368ff2cd4c17d213aab9f0845b0dc9be73?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 28 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #780      +/-   ##  
===========================================  
- Coverage    82.34%   81.97%   -0.38%       
===========================================  
  Files          117      117                
  Lines         5726     5353     -373       
===========================================  
- Hits          4715     4388     -327       
+ Misses        1011      965      -46       
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 3

> Username: mloskot  
> Created_at: 2025-12-05 19:45:50 UTC  
> Url: https://github.com/boostorg/gil/pull/780#issuecomment-3618346251  

Thank you @Processor228 !

---
