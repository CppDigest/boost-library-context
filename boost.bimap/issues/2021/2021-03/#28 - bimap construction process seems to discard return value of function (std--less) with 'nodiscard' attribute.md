# #28 - bimap construction process seems to discard return value of function (std::less) with 'nodiscard' attribute [Open]

> Username: tkingcer  
> Created at: 2021-03-30 08:05:17 UTC  
> Updated at: 2024-01-30 14:44:35 UTC  
> Url: https://github.com/boostorg/bimap/issues/28  

By using boost 1.72.0.0 c++ library and the recent release of Visual Studio 2019 (version 16.9), we found that a warning "warning C4834: discarding return value of function with 'nodiscard' attribute" appears in the bimap construction, as illustrated in the following.  
![image](https://user-images.githubusercontent.com/11314376/112953769-539b8d80-9170-11eb-817b-d7908eb065b0.png)  
  
After doing some exploarions, we found that VS 2019 16.9 set all the operators, including std::less, with [[nodiscard]] attribute in the commit [https://github.com/microsoft/STL/commit/b4af6e98f88df3a283dc2b2f1af9616684173ab4#diff-e3f9e6952fd73d97f01dd5f71b44bf0f195dc24eae335e5c0138ff25c6b559dd](url). As the bimap construction process uses std::less operator and seems to discard the returned bool value (see BOOST_concept(BinaryFunction,(Func)(Return)(First)(Second)) in concept_check.hpp), such warning thus appears.   
  
Will bimap community paln to solve this issue?

---

## Comment 1

> Username: egegumus  
> Created at: 2024-01-30 14:41:37 UTC  
> Updated at: 2024-01-30 14:44:35 UTC  
> Url: https://github.com/boostorg/bimap/issues/28#issuecomment-1917041171  

having the exact same issue with my codebase. any news on this?  
boost v1.74  
vs 2019 v16.11.28
