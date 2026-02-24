# #530 Fix redundant redeclaration when build with C++ >= 17. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2023-02-09 11:19:52 UTC  
> Updated at: 2023-02-14 18:40:38 UTC  
> Merged at: 2023-02-14 18:40:37 UTC  
> Closed at: 2023-02-14 18:40:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/530  

This fixes the following error when using C++ >= 17 and gcc 11:  
  
```  
/data/mwrep/res/osp/Boost/21-0-0-9/include/boost/multiprecision/cpp_int/literals.hpp:220:13: error: redundant redeclaration of 'constexpr' static data member 'boost::multiprecision::literals::detail::make_backend_from_pack<Pack, B>::value' [-Werror=deprecated]  
```

---

## Comment 1

> Username: mborland  
> Created_at: 2023-02-14 18:40:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/530#issuecomment-1430207160  

Looks good to me. This was deprecated in C++17 as they are now implicitly inlined.

---
