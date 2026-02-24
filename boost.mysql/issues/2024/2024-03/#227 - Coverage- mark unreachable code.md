# #227 - Coverage: mark unreachable code [Closed]

> Username: anarthal  
> Created at: 2024-03-14 11:49:22 UTC  
> Updated at: 2024-08-13 08:49:39 UTC  
> Closed at: 2024-08-13 08:49:39 UTC  
> Url: https://github.com/boostorg/mysql/issues/227  

Some statements are unreachable, but required to silence some compilers. Mark them with `__builtin_unreachable` or `std::unreachable()`.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-08-10 16:07:06 UTC  
> Url: https://github.com/boostorg/mysql/issues/227#issuecomment-2282196119  

Excluding them from coverage reports should be enough.
