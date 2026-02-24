# #509 Fixed CMakeLists INTERFACE target and added ability to skip building examples and tests [Closed]

> Username: ksergey  
> Created at: 2017-06-18 18:27:17 UTC  
> Updated at: 2017-06-19 04:25:53 UTC  
> Closed at: 2017-06-19 04:25:53 UTC  
> Url: https://github.com/boostorg/beast/pull/509  

This patch gives ability to skip build examples and tests (i.e. as subproject).  
```  
option (BEAST_BUILD_EXAMPLES "" OFF)                                                                                          
option (BEAST_BUILD_TESTS "" OFF)                                                                                             
add_subdirectory(deps/Beast)  
```  
  
Also the patch fix cmake "Beast" target. Now is enough something like below in parent CMakeLists.txt:  
```  
option (BEAST_BUILD_EXAMPLES "" OFF)                                                                                          
option (BEAST_BUILD_TESTS "" OFF)                                                                                             
add_subdirectory(deps/Beast)  
...  
add_executable(app main.cpp)  
target_linnk_libraries(app Beast)  
```  
Don't need OpenSSL in libraries and don't need to include_directories(deps/Beast/include)

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-06-18 20:19:59 UTC  
> Url: https://github.com/boostorg/beast/pull/509#issuecomment-309300415  

There have been significant changes to the CMakeLists.txt in **v61** have you rebased?

---

## Comment 2

> Username: ksergey  
> Created_at: 2017-06-18 21:28:35 UTC  
> Url: https://github.com/boostorg/beast/pull/509#issuecomment-309304125  

Changes for v61 branch #511

---
