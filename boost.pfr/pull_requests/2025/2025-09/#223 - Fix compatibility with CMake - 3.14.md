# #223 Fix compatibility with CMake < 3.14 [Merged]

> Username: Flamefire  
> Created at: 2025-09-30 12:27:14 UTC  
> Updated at: 2025-10-07 08:54:35 UTC  
> Merged at: 2025-10-06 16:38:26 UTC  
> Closed at: 2025-10-06 16:38:26 UTC  
> Url: https://github.com/boostorg/pfr/pull/223  

`NAME_WLE` was introduced in 3.14  
  
I'm quite sure that "longest" and "last" extension for all tests are the same but please double check.  
  
BTW: The 2 `target_include_directories(pfr_core_${testname} PRIVATE ../../../)` look suspicious. Why are they required when you already link to `Boost::core` and `Boost::pfr`?

---

## Comment 1

> Username: coveralls  
> Created_at: 2025-09-30 12:36:18 UTC  
> Url: https://github.com/boostorg/pfr/pull/223#issuecomment-3351870527  

## Pull Request Test Coverage Report for [Build 18129831217](https://coveralls.io/builds/75755640)  
  
  
### Details  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/75755640/badge)](https://coveralls.io/builds/75755640) |  
| :-- | --: |  
| Change from base [Build 17710819055](https://coveralls.io/builds/75532713): |  0.0% |  
| Covered Lines: | 405 |  
| Relevant Lines: | 405 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Comment 2

> Username: apolukhin  
> Created_at: 2025-10-06 16:38:38 UTC  
> Url: https://github.com/boostorg/pfr/pull/223#issuecomment-3372737460  

Many thanks for the fix!

---
