# #67 - On Linux, Boost dll requires linking against dl, why not do this in the CMakeLists.txt? [Closed]

> Username: joris-nijs  
> Created at: 2023-12-20 13:37:56 UTC  
> Updated at: 2024-12-21 12:36:28 UTC  
> Closed at: 2024-12-21 12:36:14 UTC  
> Url: https://github.com/boostorg/dll/issues/67  

The boost dll documentation documentation suggests to link against dl on Linux: [https://www.boost.org/doc/libs/1_84_0/doc/html/boost_dll/getting_started.html](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_dll/getting_started.html).  
  
Would there be reason not to do this already in the Boost dll CMakeLists.txt when using the CMake integration?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-12-21 12:36:27 UTC  
> Url: https://github.com/boostorg/dll/issues/67#issuecomment-2558108654  

Many thanks for the report! Fixed
