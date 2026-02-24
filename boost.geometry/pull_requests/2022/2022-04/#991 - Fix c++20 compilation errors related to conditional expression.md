# #991 Fix c++20 compilation errors related to conditional expression [Merged]

> Username: stefan301  
> Created at: 2022-04-11 14:21:17 UTC  
> Updated at: 2022-04-12 16:46:38 UTC  
> Merged at: 2022-04-12 10:59:54 UTC  
> Closed at: 2022-04-12 10:59:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/991  

Fix c++20 compilation errors related to conditional expression with int and floating_point_type  
  
A floating_point_type with implicit conversions can lead to this C++20 compilation error:  
  
boost\geometry\policies\robustness\segment_ratio.hpp(232,1): error C2445: result type of conditional expression is ambiguous: types 'int' and '<floating_point_type>' can be converted to multiple common types

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-04-11 14:24:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/991#issuecomment-1095117922  

Thanks!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2022-04-12 06:49:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/991#pullrequestreview-938997162  

Thanks!

---
