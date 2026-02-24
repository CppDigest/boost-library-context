# #22 CMake: Add install target [Closed]

> Username: jschueller  
> Created at: 2024-03-04 09:09:45 UTC  
> Updated at: 2024-07-04 14:00:22 UTC  
> Closed at: 2024-07-04 14:00:20 UTC  
> Url: https://github.com/boostorg/array/pull/22  

/cc @pdimov @mclow

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-07-04 13:58:15 UTC  
> Url: https://github.com/boostorg/array/pull/22#issuecomment-2209065480  

No, Boost libraries don't support "standalone" installation. You can install a specific library (such as Array) using the superproject with  
  
```  
cmake -DBOOST_INCLUDE_LIBRARIES=array -DCMAKE_INSTALL_PREFIX=... ..  
cmake --build . --target install  
```

---

## Comment 2

> Username: jschueller  
> Created_at: 2024-07-04 14:00:19 UTC  
> Url: https://github.com/boostorg/array/pull/22#issuecomment-2209073526  

ok

---
