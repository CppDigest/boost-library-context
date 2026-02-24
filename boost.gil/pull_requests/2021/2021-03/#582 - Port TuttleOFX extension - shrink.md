# #582 Port TuttleOFX extension : shrink [Open]

> Username: meshtag  
> Created at: 2021-03-17 08:01:50 UTC  
> Updated at: 2025-09-05 19:15:40 UTC  
> Url: https://github.com/boostorg/gil/pull/582  

### Description  
  
This pull request intends to port ```shrink``` extension from [TuttleOFX repository](https://github.com/tuttleofx/TuttleOFX/tree/develop/libraries/boostHack/boost/gil/extension/toolbox) to Gil as mentioned in #449 .  
  
I have tried not to change anything significant in actual extension code. However, I have reformatted test cases completely in order to align them with current practices followed by Gil.  
  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2021-03-17 09:32:39 UTC  
> Updated_at: 2025-09-05 19:15:40 UTC  
> Url: https://github.com/boostorg/gil/pull/582#issuecomment-800935792  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/582?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 78.79%. Comparing base ([`bbdce36`](https://app.codecov.io/gh/boostorg/gil/commit/bbdce36a1f655ebf8332b775790673b197c8f1de?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`06ea1ff`](https://app.codecov.io/gh/boostorg/gil/commit/06ea1ff7fa7d001ecace0631ef67c46ecaa27980?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 175 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #582      +/-   ##  
===========================================  
+ Coverage    78.72%   78.79%   +0.07%       
===========================================  
  Files          118      119       +1       
  Lines         5034     5051      +17       
===========================================  
+ Hits          3963     3980      +17       
  Misses        1071     1071                
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: mloskot  
> Created_at: 2021-03-24 09:40:26 UTC  
> Url: https://github.com/boostorg/gil/pull/582#issuecomment-805651365  

@meshtag You can try our https://github.com/boostorg/gil/tree/develop/example/clang-format

---

## Review 3 [Changes requested]

> Username: mloskot  
> Created_at: 2021-04-09 19:57:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/582#pullrequestreview-632745026  

📁 include/boost/gil/extension/toolbox/metafunctions/shrink.hpp

```diff
   4 |+ 
   5 |+ #ifndef _shrink_hpp_
   6 |+ #define _shrink_hpp_
```

> Username: mloskot  
> Created_at: 2021-04-09 19:57:07 UTC  
> Updated_at: 2021-04-10 04:16:48 UTC  
> Url: https://github.com/boostorg/gil/pull/582#discussion_r610873923  

These header guards need to be changed, see https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md#guidelines

---

📁 include/boost/gil/extension/toolbox/metafunctions/shrink.hpp

```diff
  13 |+ /// destination/resultant view given as second parameter to the overloaded '()' operator.
  14 |+ /// The dimensions of the smaller view are specified by struct constructor parameters.
  15 |+ template <typename view_t>
```

> Username: mloskot  
> Created_at: 2021-04-09 19:57:18 UTC  
> Updated_at: 2021-04-10 04:16:48 UTC  
> Url: https://github.com/boostorg/gil/pull/582#discussion_r610874018  

Could you rename template parameters to upper case, `View` ?

---

📁 include/boost/gil/extension/toolbox/metafunctions/shrink.hpp

```diff
  41 |+ 
  42 |+         double width = view.width() - left - right;
  43 |+         double height = view.height() - top - bottom;
```

> Username: mloskot  
> Created_at: 2021-04-09 19:57:32 UTC  
> Updated_at: 2021-04-10 04:16:48 UTC  
> Url: https://github.com/boostorg/gil/pull/582#discussion_r610874136  

Some original code review: if not modified, make it const


📁 test/extension/toolbox/CMakeLists.txt

```diff
  27 |-   subchroma_image)
  27 |+   subchroma_image
  28 |+   shrink)
```

> Username: mloskot  
> Created_at: 2021-04-09 19:57:43 UTC  
> Updated_at: 2021-04-10 04:16:48 UTC  
> Url: https://github.com/boostorg/gil/pull/582#discussion_r610874245  

Could you sort it alphabetically?


---
