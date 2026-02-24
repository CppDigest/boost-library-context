# #81 - Removal of all.hpp breaks CMake [Closed]

> Username: djarek  
> Created at: 2018-05-18 21:18:49 UTC  
> Updated at: 2018-05-23 08:27:34 UTC  
> Closed at: 2018-05-23 08:27:34 UTC  
> Url: https://github.com/boostorg/context/issues/81  

While this is not strictly a Boost issue, but this commit https://github.com/boostorg/context/commit/2e37599461912cc1679f106a25456a493f7f0b27 breaks FindBoost.cmake, because this script uses `context/all.hpp` to detect the presence of Boost.Context headers (if missing, it concludes Boost.Context is not present in the system).  
  
Example:  
```  
find_package(Boost 1.67  
             COMPONENTS  
                fiber  
                context  
             REQUIRED)  
```  
  
Relevant FindBoost.cmake line:  
https://github.com/Kitware/CMake/blob/v3.11.0/Modules/FindBoost.cmake#L875

---

## Comment 1

> Username: olk  
> Created at: 2018-05-23 08:27:33 UTC  
> Url: https://github.com/boostorg/context/issues/81#issuecomment-391264347  

it was necessary to remove `context/all.hpp`, use `context/detail/fcontext.hpp` in FindBoost.cmake instead
