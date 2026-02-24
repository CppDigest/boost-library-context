# #1083 Update minimum CMake version to 3.9 [Closed]

> Username: Flamefire  
> Created at: 2025-09-11 07:28:29 UTC  
> Updated at: 2025-09-11 11:40:23 UTC  
> Closed at: 2025-09-11 11:40:17 UTC  
> Url: https://github.com/boostorg/boost/pull/1083  

BoostRoot uses `CMAKE_MATCH_<n>` which only exists since CMake 3.9  
  
https://cmake.org/cmake/help/latest/command/if.html#matches  
  
@pdimov

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-09-11 09:17:48 UTC  
> Url: https://github.com/boostorg/boost/pull/1083#issuecomment-3279407950  

I wonder how did that work before.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-09-11 09:22:14 UTC  
> Url: https://github.com/boostorg/boost/pull/1083#issuecomment-3279431177  

No one used CMake < 3.9 for the super build I guess. Or it just skipped stuff or added everything as the variable was unset

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-09-11 10:40:38 UTC  
> Url: https://github.com/boostorg/boost/pull/1083#issuecomment-3279851014  

@grisumbras reports that 3.8 works: https://github.com/boostorg/json/pull/1110

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-09-11 11:40:17 UTC  
> Url: https://github.com/boostorg/boost/pull/1083#issuecomment-3280136258  

Can we merge https://github.com/boostorg/boost/pull/1082 to verify the used CMake version?  
  
In this case: It is a documentation bug. That variables were supported since 2.6

---
