# #469 Dinkumware: disable C++ 98 binders without adaptor typedefs [Merged]

> Username: MarcelRaad  
> Created at: 2023-01-21 13:07:17 UTC  
> Updated at: 2023-01-21 20:24:44 UTC  
> Merged at: 2023-01-21 20:08:08 UTC  
> Closed at: 2023-01-21 20:08:08 UTC  
> Url: https://github.com/boostorg/config/pull/469  

When building with MSVC in C++ 20 mode with `_HAS_AUTO_PTR_ETC` explicitly enabled, `std:bind1st` and `std::bind2nd` are unavailable unless `_HAS_DEPRECATED_ADAPTOR_TYPEDEFS` is also explicitly enabled.

---
