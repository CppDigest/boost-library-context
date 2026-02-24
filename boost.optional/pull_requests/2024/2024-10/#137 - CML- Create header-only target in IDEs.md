# #137 CML: Create header-only target in IDEs [Merged]

> Username: Flamefire  
> Created at: 2024-10-04 18:07:24 UTC  
> Updated at: 2024-10-05 07:59:42 UTC  
> Merged at: 2024-10-05 05:39:13 UTC  
> Closed at: 2024-10-05 05:39:13 UTC  
> Url: https://github.com/boostorg/optional/pull/137  

Since CMake 3.19 it is possible to specify private sources for `INTERFACE` targets. This makes the library turn up like compiled libraries in IDEs such as Visual Studio.  
  
It can also be specified in the `add_library` call but using `target_sources` is more isolated to the CMake version check

---

## Comment 1

> Username: typenametea  
> Created_at: 2024-10-04 19:13:14 UTC  
> Url: https://github.com/boostorg/optional/pull/137#issuecomment-2394405838  

This makes the optional library appear in Visual Studio for me, thanks!

---
