# #295 Fix Sub::operator== signature to avoid ambiguity errors in C++20 [Merged]

> Username: pdimov  
> Created at: 2023-08-25 23:02:07 UTC  
> Updated at: 2023-08-27 15:55:41 UTC  
> Merged at: 2023-08-27 15:55:41 UTC  
> Closed at: 2023-08-27 15:55:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/295  

`Sub::operator==` takes a non-const reference by mistake, which causes ambiguity errors under msvc-14.3 with cxxstd=20.

---
