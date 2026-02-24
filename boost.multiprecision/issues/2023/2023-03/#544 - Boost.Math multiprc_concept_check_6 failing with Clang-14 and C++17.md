# #544 - Boost.Math multiprc_concept_check_6 failing with Clang-14 and C++17 [Closed]

> Username: mborland  
> Created at: 2023-03-17 14:40:55 UTC  
> Updated at: 2023-04-21 17:40:54 UTC  
> Closed at: 2023-04-21 17:40:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/544  

Under Clang-14 with C++17 there is a new error here:  https://github.com/boostorg/math/actions/runs/4447933117/jobs/7810075798#step:17:1835 saying that number base is an incomplete type. It doesn't look like any other version of clang is effected https://drone.cpp.al/boostorg/math/1267.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-04-21 17:40:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/544#issuecomment-1518145435  

The code that caused this has been reverted, should be fixed now.
