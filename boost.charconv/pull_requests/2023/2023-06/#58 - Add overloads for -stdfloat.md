# #58 Add overloads for <stdfloat> [Merged]

> Username: mborland  
> Created at: 2023-06-05 14:31:15 UTC  
> Updated at: 2023-06-12 09:24:12 UTC  
> Merged at: 2023-06-12 09:24:09 UTC  
> Closed at: 2023-06-12 09:24:09 UTC  
> Url: https://github.com/boostorg/charconv/pull/58  

`std::float128_t` will be added when `__float128` support is available.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-06-07 11:25:44 UTC  
> Url: https://github.com/boostorg/charconv/pull/58#issuecomment-1580587473  

@pdimov When you get a chance can you please look through this? For the 16-bit types I used `float` as an interchange format. The comment about excess precision only caused failure when running in 32-bit mode.

---
