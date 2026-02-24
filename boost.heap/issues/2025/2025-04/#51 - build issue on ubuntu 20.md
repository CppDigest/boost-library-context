# #51 - build issue on ubuntu 20 [Closed]

> Username: greg7mdp  
> Created at: 2025-04-17 21:49:48 UTC  
> Updated at: 2025-04-21 19:45:49 UTC  
> Closed at: 2025-04-17 23:29:50 UTC  
> Url: https://github.com/boostorg/heap/issues/51  

```  
CMake Error at libraries/boost/libs/heap/CMakeLists.txt:22 (set_target_properties):  INTERFACE_LIBRARY targets may only have whitelisted properties.  The property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
```  
  
Same issue as https://github.com/boostorg/lockfree/issues/114.  
  
Fixed with: https://github.com/boostorg/lockfree/commit/f2965389061ef3cdd8c30035546f6630db416907

---

## Comment 1

> Username: greg7mdp  
> Created at: 2025-04-18 00:14:34 UTC  
> Url: https://github.com/boostorg/heap/issues/51#issuecomment-2814235976  

Thanks for the quick fix. Will it be picked into https://github.com/boostorg/boost/tree/master soon?

---

## Comment 2

> Username: timblechmann  
> Created at: 2025-04-18 00:16:08 UTC  
> Url: https://github.com/boostorg/heap/issues/51#issuecomment-2814237401  

yes, it should land on heap/master soonish

---

## Comment 3

> Username: greg7mdp  
> Created at: 2025-04-21 19:44:50 UTC  
> Updated at: 2025-04-21 19:45:48 UTC  
> Url: https://github.com/boostorg/heap/issues/51#issuecomment-2819356791  

> yes, it should land on heap/master soonish  
  
let's go :-). I need it in `boostorg/boost` master as I submodule the whole boost.
