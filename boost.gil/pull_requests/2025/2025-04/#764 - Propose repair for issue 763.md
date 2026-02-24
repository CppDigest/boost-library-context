# #764 Propose repair for issue 763 [Merged]

> Username: ckormanyos  
> Created at: 2025-04-18 17:43:19 UTC  
> Updated at: 2025-04-19 07:54:09 UTC  
> Merged at: 2025-04-18 19:59:35 UTC  
> Closed at: 2025-04-18 19:59:35 UTC  
> Url: https://github.com/boostorg/gil/pull/764  

This is intended to address #763  
  
### Tasklist  
  
Required:  
  
- [ ] Ensure all CI builds pass

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-04-18 17:49:18 UTC  
> Updated_at: 2025-04-18 20:00:05 UTC  
> Url: https://github.com/boostorg/gil/pull/764#issuecomment-2815923405  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/764?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 82.34%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`dcce964`)](https://app.codecov.io/gh/boostorg/gil/commit/dcce96467ab6bddfa4fcfb1f4c91e2df40174861?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 21 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #764      +/-   ##  
===========================================  
+ Coverage    82.12%   82.34%   +0.21%       
===========================================  
  Files          117      117                
  Lines         5355     5726     +371       
===========================================  
+ Hits          4398     4715     +317       
- Misses         957     1011      +54       
```  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2025-04-18 19:44:35 UTC  
> Url: https://github.com/boostorg/gil/pull/764#issuecomment-2816093789  

CI is (and has been) broken on clang 5 and macos 13 for a few months. I can't see tha right conversion off the top of my head.  
  
Intuitively, I'd think it would make sense to fix CI, but I can'tfigure out exactly how to do that on my own.

---

## Comment 3

> Username: mloskot  
> Created_at: 2025-04-18 19:58:56 UTC  
> Url: https://github.com/boostorg/gil/pull/764#issuecomment-2816108964  

Thanks a lot for helping out with this!

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2025-04-18 20:18:14 UTC  
> Url: https://github.com/boostorg/gil/pull/764#issuecomment-2816130109  

Thank you @mloskot

---

## Comment 5

> Username: sdebionne  
> Created_at: 2025-04-19 07:54:08 UTC  
> Url: https://github.com/boostorg/gil/pull/764#issuecomment-2816596674  

Thank you for the fix and sorry for the mess.

---
