# #910 - target_compile_features specified unknown feature [Closed]

> Username: alandefreitas  
> Created at: 2023-06-21 22:31:03 UTC  
> Updated at: 2023-06-23 09:21:59 UTC  
> Closed at: 2023-06-23 09:21:59 UTC  
> Url: https://github.com/boostorg/json/issues/910  

### Version of Boost  
  
develop  
  
### Steps necessary to reproduce the problem  
  
We just started getting this error in CI:  
  
```  
CMake Error at libs/json/CMakeLists.txt:57 (target_compile_features):  
  target_compile_features specified unknown feature "_SCL_SECURE_NO_WARNINGS"  
  for target "boost_json".  
```  
  
The complete output: https://github.com/boostorg/url/actions/runs/5336814814  
  
### All relevant compiler information  
  
The error happens in MSVC 14.34, MSVC 14.29, MinGW, and clang-cl.

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-06-22 05:33:05 UTC  
> Url: https://github.com/boostorg/json/issues/910#issuecomment-1602035617  

Oops, that was a bad copypaste on my part.

---

## Comment 2

> Username: grisumbras  
> Created at: 2023-06-22 05:54:36 UTC  
> Url: https://github.com/boostorg/json/issues/910#issuecomment-1602049313  

Can you check if #911 fixes this?

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-06-22 12:55:27 UTC  
> Url: https://github.com/boostorg/json/issues/910#issuecomment-1602587525  

I had a look at #911, if `target_compile_definitions(${target} PRIVATE _SCL_SECURE_NO_WARNINGS)` doesn't fix the original problem, it sure won't cause other people this problem (#910). The definition is even private.
