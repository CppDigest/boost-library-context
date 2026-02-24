# #194 fix(iterator): remove deprecated inheritance from std::iterator (#97) [Merged]

> Username: bassoy  
> Created at: 2024-05-05 15:41:18 UTC  
> Updated at: 2024-05-07 20:32:44 UTC  
> Merged at: 2024-05-07 20:32:43 UTC  
> Closed at: 2024-05-07 20:32:43 UTC  
> Url: https://github.com/boostorg/ublas/pull/194  

`std::iterator` was deprecated in C++17 and removed in C++20. I replaced the inheritance with the 5 equivalent typedefs, even though they're not all used by ublas, for compatibility in case clients depend on them.  
  
Fixes issue https://github.com/boostorg/ublas/issues/176

---
