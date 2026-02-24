# #183 Avoid `-Wzero-as-null-pointer-constant` warning in string_view ctor [Merged]

> Username: Flamefire  
> Created at: 2024-12-31 12:08:18 UTC  
> Updated at: 2025-01-05 16:42:54 UTC  
> Merged at: 2025-01-05 15:25:14 UTC  
> Closed at: 2025-01-05 15:25:14 UTC  
> Url: https://github.com/boostorg/core/pull/183  

This is an issue for C++11 and up for e.g. Boost libs that want to ensure warning-free build.  
  
Solution is simply to move the SFINAE expression to the template argument list to avoid the `<..>* = 0` default argument  
  
Alternative: `enable_if<..., int>::type = 0`

---
