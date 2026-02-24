# #251 Fix required CMake version [Merged]

> Username: Flamefire  
> Created at: 2025-10-01 08:20:46 UTC  
> Updated at: 2025-10-01 14:53:19 UTC  
> Merged at: 2025-10-01 14:41:36 UTC  
> Closed at: 2025-10-01 14:41:36 UTC  
> Url: https://github.com/boostorg/log/pull/251  

`cxx_std_11` requires CMake 3.8

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-10-01 10:14:40 UTC  
> Url: https://github.com/boostorg/log/pull/251#issuecomment-3355650690  

`cxx_std_11` is not used in Boost.Log. Why is this needed?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-01 10:41:10 UTC  
> Url: https://github.com/boostorg/log/pull/251#issuecomment-3355740857  

You are right, I was going by the error message: https://github.com/Flamefire/cmake/actions/runs/18138718785/job/51623991053#step:8:1792  
> CMake Error in libs/log/CMakeLists.txt:  
  Specified unknown feature "cxx_std_11" for target "boost_log_setup".  
  
It seems CMake reports this when it was "inherited" too. I.e. some dependency sets this feature, with `target_link_libraries` it gets added to `boost_log` during the configure step and the generation step then trips over it.  
  
So while this is (currently) not strictly required here in practice it is a requirement.  
  
BTW: It should actually be used here. https://github.com/boostorg/log/commit/2352b7eacfdf7993db67698679272f01c4b00606 declares the library as C++11 and the [Jamfile](https://github.com/boostorg/log/blob/a46993b0b8feb1da54566bbb306f1a7f2e793c4e/build/Jamfile.v2#L65) has the requirement too.

---

## Comment 3

> Username: Lastique  
> Created_at: 2025-10-01 14:43:57 UTC  
> Url: https://github.com/boostorg/log/pull/251#issuecomment-3356775096  

> It should actually be used here.  
  
C++11 features are requested here:  
  
https://github.com/boostorg/log/blob/3ab24d1e4f3b621d6d031d5c47c540f453d1f8be/CMakeLists.txt#L462-L467  
https://github.com/boostorg/log/blob/3ab24d1e4f3b621d6d031d5c47c540f453d1f8be/CMakeLists.txt#L615-L621  
  
Thanks for the fix.

---
