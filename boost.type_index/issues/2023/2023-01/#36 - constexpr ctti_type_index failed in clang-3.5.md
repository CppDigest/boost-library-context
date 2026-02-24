# #36 - constexpr ctti_type_index failed in clang-3.5 [Closed]

> Username: denzor200  
> Created at: 2023-01-26 13:01:20 UTC  
> Updated at: 2025-05-19 15:54:56 UTC  
> Closed at: 2025-05-19 15:54:55 UTC  
> Url: https://github.com/boostorg/type_index/issues/36  

https://godbolt.org/z/6P9bv51z7

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-17 09:45:56 UTC  
> Url: https://github.com/boostorg/type_index/issues/36#issuecomment-1949918240  

Yes, it's a clang limitation that has been changed in 3.6. I'm not aware of a workaround for that

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-05-19 15:54:55 UTC  
> Url: https://github.com/boostorg/type_index/issues/36#issuecomment-2891525594  

The compiler is too old. The issue would not fix
