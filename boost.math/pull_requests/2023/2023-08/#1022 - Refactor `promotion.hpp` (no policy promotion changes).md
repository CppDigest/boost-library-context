# #1022 Refactor `promotion.hpp` (no policy promotion changes) [Closed]

> Username: ryanelandt  
> Created at: 2023-08-27 15:44:19 UTC  
> Updated at: 2024-08-15 15:41:42 UTC  
> Closed at: 2024-08-15 15:41:41 UTC  
> Url: https://github.com/boostorg/math/pull/1022  

**Edit:** #1021 was closed because it introduced misguided policy promotion changes. This PR contains the ideas from #1021 that are not problematic.  
  
-----------  
   
This PR includes the following features of #1021   
- Use of variadic templates instead of manual emulation with optional `float` arguments  
- Use of `std::common_type` for float_type promotion simplification  
  
This PR does not including the following features of #1021   
- Support for 16 bit floating types  
  
The file `promotion.hpp` in this PR is identical to that in #1021 except for line 91. In #1021 this reads:  
```c++  
using type = std::common_type_t<T1, T2>;  
```  
In this PR it reads:  
```c++  
using type = std::common_type_t<T1, T2, float>;  
```  
This preserves the current floating point promotion behavior.

---

## Comment 1

> Username: mborland  
> Created_at: 2024-08-15 15:41:41 UTC  
> Url: https://github.com/boostorg/math/pull/1022#issuecomment-2291567368  

Merged in with 1179

---
