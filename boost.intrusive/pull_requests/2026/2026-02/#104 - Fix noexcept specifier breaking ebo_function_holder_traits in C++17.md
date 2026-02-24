# #104 Fix noexcept specifier breaking ebo_function_holder_traits in C++17. [Merged]

> Username: nkolotov  
> Created at: 2026-02-16 13:09:44 UTC  
> Updated at: 2026-02-16 18:31:20 UTC  
> Merged at: 2026-02-16 18:31:10 UTC  
> Closed at: 2026-02-16 18:31:10 UTC  
> Url: https://github.com/boostorg/intrusive/pull/104  

Fix for #103   
  
Extended traits to deal with `noexcept`  in C++17 mode.  
Added some extra tests involving invocations of dispose functions with and without `noexcept` specifier.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2026-02-16 18:31:20 UTC  
> Url: https://github.com/boostorg/intrusive/pull/104#issuecomment-3909959357  

Many thanks for the fix

---
