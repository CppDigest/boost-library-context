# #392 - Fix Case Sensitivity for Windows.h and Advapi32 [Closed]

> Username: Jackarain  
> Created at: 2024-07-29 15:44:11 UTC  
> Updated at: 2024-07-31 00:54:27 UTC  
> Closed at: 2024-07-31 00:54:27 UTC  
> Url: https://github.com/boostorg/process/issues/392  

Hello,  
  
I encountered a compilation error while cross-compiling in a mingw64 environment on a Linux system due to case sensitivity issues. The header file windows.h is included with the wrong case in utf8.cpp:  
  
https://github.com/boostorg/process/blob/755a3ec78d36b903a6556817562aac5d0244f017/src/detail/utf8.cpp#L12  
  
Please change it to:  
  
`#include <windows.h>`  
  
Additionally, in CMakeLists.txt, the target name should be lowercase:  
  
https://github.com/boostorg/process/blob/755a3ec78d36b903a6556817562aac5d0244f017/CMakeLists.txt#L60  
  
  
This is necessary because, on Linux systems, mingw64's windows.h/Advapi32.lib is in lowercase.  
  
It might be worth checking other parts of the Boost libraries for similar case sensitivity issues.  
  
Thank you!
