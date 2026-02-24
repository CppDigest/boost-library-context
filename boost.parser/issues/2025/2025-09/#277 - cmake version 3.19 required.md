# #277 - cmake version 3.19 required? [Closed]

> Username: nigels-com  
> Created at: 2025-09-05 23:57:15 UTC  
> Updated at: 2025-09-06 21:55:55 UTC  
> Closed at: 2025-09-06 21:55:55 UTC  
> Url: https://github.com/boostorg/parser/issues/277  

I happened to be [poking around with cmake builds and old versions of cmake](https://github.com/boostorg/nowide/pull/199) in the context of nowide.  
  
Parser builds for me on Ubuntu 24.04 with cmake-3.19.0, but not cmake-3.18.0.  
And in CMakeLists.txt it's implied that 3.14 ought to work?  
  
```  
cmake_minimum_required(VERSION 3.14...3.20)  
```  
  
The problem concerns INTERFACE_LIBRARY  
  
```  
$ git clean -xdf . && PATH=/opt/cmake-3.18.0-Linux-x86_64/bin:$PATH cmake .. -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=Y -DCMAKE_CXX_COMPILER=clang++-19 -DBOOST_INCLUDE_LIBRARIES=parser && ninja  
...  
CMake Error at libs/heap/CMakeLists.txt:22 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CMAKE_CXX_STANDARD_REQUIRED" is not allowed.  
...  
CMake Error at libs/lockfree/CMakeLists.txt:45 (set_target_properties):  
  INTERFACE_LIBRARY targets may only have whitelisted properties.  The  
  property "CXX_STANDARD_REQUIRED" is not allowed.  
...  
```  
  
So I do wonder if if boost can broadly agree on cmake-3.19.0 as a baseline that works, and we should collectively say that previous versions are simply not supported?  
  
Should parse be requiring 3.19 rather than 3.14?

---

## Comment 1

> Username: Flamefire  
> Created at: 2025-09-06 10:51:50 UTC  
> Url: https://github.com/boostorg/parser/issues/277#issuecomment-3261833807  

This is an issue of Boost.Heap and Boost.Lockfree not Boost.Parser. It is already fixed in the latter

---

## Comment 2

> Username: nigels-com  
> Created at: 2025-09-06 21:55:55 UTC  
> Url: https://github.com/boostorg/parser/issues/277#issuecomment-3263242873  

Indeed.  Resolved via [boostorg/heap#55](https://github.com/boostorg/heap/pull/55) and [boostorg/lockfree#122](https://github.com/boostorg/lockfree/pull/122)  
  
cmake-3.14.0 is working now for parser.
