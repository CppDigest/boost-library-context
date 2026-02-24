# #50 Avoid UB in debug code [Open]

> Username: Flamefire  
> Created at: 2024-09-17 17:58:11 UTC  
> Updated at: 2024-09-17 17:58:11 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/50  

`std::memset` calls with a nullptr argument are flagged as UB by GCC `-fsanitize=undefined`, so check the size first  
  
See https://godbolt.org/z/Kjch8dd1d for a simple example showing the diagnostics  
  
Fixes #39

---
