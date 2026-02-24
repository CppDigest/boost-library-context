# #1056 Fix c++20 compilation errors related to conditional expression [Merged]

> Username: stefan301  
> Created at: 2022-08-20 14:26:28 UTC  
> Updated at: 2022-09-01 08:28:09 UTC  
> Merged at: 2022-09-01 08:28:08 UTC  
> Closed at: 2022-09-01 08:28:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1056  

Fix c++20 compilation errors related to conditional expression with int and floating_point_type   
  
A floating_point_type with implicit conversions can lead to this C++20 compilation error:  
  
boost\boost\geometry\algorithms\detail\overlay\get_turn_info.hpp(233,1): error C2445: result type of conditional expression is ambiguous: types '<floating_point_type>' and 'int' can be converted to multiple common types

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2022-08-24 08:12:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1056#pullrequestreview-1083355278  

Thanks!

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-09-01 08:27:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1056#pullrequestreview-1092977668  

Thanks

---
