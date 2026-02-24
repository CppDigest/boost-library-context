# #826 Modernize `x3::repeat` [Merged]

> Username: saki7  
> Created at: 2025-09-10 02:42:19 UTC  
> Updated at: 2025-09-10 02:51:55 UTC  
> Merged at: 2025-09-10 02:50:50 UTC  
> Closed at: 2025-09-10 02:50:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/826  

Part of boostorg/spirit_x4#4   
  
- Use concepts in `x3::repeat`.  
- `x3::repeat` is now a CPO that inhibits ADL.  
- `x3::inf` is deprecated regarding overly generic name; use `x3::repeat_inf` instead.  
- `x3::repeat[p]` is deprecated since it has the exact same meaning as `*p`. It is generally discouraged to provide multiple ways to achieve same the functionality in modern C++ library design.

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-10 02:51:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/826#issuecomment-3273017561  

Self-merged. Although I added some deprecations, they are backward compatible as of now.

---
