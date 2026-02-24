# #246 - CMAKE_SIZEOF_VOID_P is empty when trying to build [Closed]

> Username: faust403  
> Created at: 2023-11-27 18:07:40 UTC  
> Updated at: 2024-03-10 19:31:17 UTC  
> Closed at: 2024-03-10 19:31:17 UTC  
> Url: https://github.com/boostorg/context/issues/246  

When I'm trying to build getting this error:  
  
`  
CMake Error at libs/context/CMakeLists.txt:29 (math):  
  math cannot parse the expression: "*8": syntax error, unexpected exp_TIMES  
  (1).  
`  
  
So, this line is:  
  
`math(EXPR _bits "${CMAKE_SIZEOF_VOID_P}*8")`  
  
It only means that CMAKE_SIZEOF_VOID_P isn't setted. But it is when project() called - it tries all stuff including compiler and setting appropriate variables. So simple removing LANGUAGES CXX from project() call fix this issue(idk why it depends, one would be safe to assume it's more related with cmake, not with this project)  
  
cmake version 3.27.8

---

## Comment 1

> Username: olk  
> Created at: 2024-03-10 19:31:17 UTC  
> Url: https://github.com/boostorg/context/issues/246#issuecomment-1987339380  

no code at line 29 in CMakeLists.txt
