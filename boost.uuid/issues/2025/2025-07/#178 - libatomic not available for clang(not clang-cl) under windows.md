# #178 - libatomic not available for clang(not clang-cl) under windows [Closed]

> Username: marakew  
> Created at: 2025-07-19 22:12:09 UTC  
> Updated at: 2025-07-22 17:39:21 UTC  
> Closed at: 2025-07-22 17:39:21 UTC  
> Url: https://github.com/boostorg/uuid/issues/178  

https://github.com/boostorg/uuid/blob/689f3d2c152626b833a2fb2d636516ef126fa300/CMakeLists.txt#L35  
  
```  
- if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang" AND NOT CMAKE_CXX_COMPILER_FRONTEND_VARIANT STREQUAL "MSVC")  
+ if(NOT WIN32 AND CMAKE_CXX_COMPILER_ID STREQUAL "Clang" AND NOT CMAKE_CXX_COMPILER_FRONTEND_VARIANT STREQUAL "MSVC")  
```  
  
could you please fix this ?  
clang.exe can be using under windows  
but libatomic doest not exists for windows  
thanks
