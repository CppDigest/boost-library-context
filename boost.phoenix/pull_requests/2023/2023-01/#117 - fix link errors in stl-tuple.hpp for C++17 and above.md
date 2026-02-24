# #117 fix link errors in stl/tuple.hpp for C++17 and above [Closed]

> Username: tobias-loew  
> Created at: 2023-01-09 10:27:05 UTC  
> Updated at: 2023-01-12 17:44:06 UTC  
> Closed at: 2023-01-12 17:44:06 UTC  
> Url: https://github.com/boostorg/phoenix/pull/117  

The new variables "uarg..." in stl/tuple.hpp lead to linker warnings, when included in more than one cpp-file. Here is a fix using inline variables for C++17 and above. C++14 is not fixed yet!

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2023-01-12 17:44:05 UTC  
> Url: https://github.com/boostorg/phoenix/pull/117#issuecomment-1380776581  

targets same issue as #116

---
