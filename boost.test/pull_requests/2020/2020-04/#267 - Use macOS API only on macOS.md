# #267 Use macOS API only on macOS [Merged]

> Username: pinotree  
> Created at: 2020-04-20 06:33:21 UTC  
> Updated at: 2022-02-27 19:46:35 UTC  
> Merged at: 2020-05-09 08:37:58 UTC  
> Closed at: 2020-05-09 08:37:59 UTC  
> Url: https://github.com/boostorg/test/pull/267  

`__MACH__` matches any Mach-based OS, which means both macOS and GNU/Hurd; since the API used is really specific to macOS, then also check for `__APPLE__`.  
  
This fixes the build on GNU/Hurd.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2020-05-17 10:03:52 UTC  
> Url: https://github.com/boostorg/test/pull/267#issuecomment-629772812  

in master

---
