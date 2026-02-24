# #132 - Add support for MSVC STL/stdlib [Closed]

> Username: BurningEnlightenment  
> Created at: 2023-08-17 06:15:22 UTC  
> Updated at: 2023-10-31 12:01:24 UTC  
> Closed at: 2023-10-31 12:01:24 UTC  
> Url: https://github.com/boostorg/predef/issues/132  

The [MSVC stdlib](https://github.com/microsoft/STL) has long branched off of Dinkumware and defines their own version macros:  
```c  
#define _CPPLIB_VER       650  
#define _MSVC_STL_VERSION 143  
#define _MSVC_STL_UPDATE  202302L  
```  
https://github.com/microsoft/STL/blame/8674b3d732a2bae43105fb7654d3b8c1c57adb56/stl/inc/yvals_core.h#L875-L876C33  
  
Whereas the `_CPPLIB_VER` has not been incremented since the project going open source.
