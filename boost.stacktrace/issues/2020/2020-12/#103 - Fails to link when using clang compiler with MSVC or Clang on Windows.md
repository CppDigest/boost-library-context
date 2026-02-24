# #103 - Fails to link when using clang compiler with MSVC or Clang on Windows [Closed]

> Username: tan-wei  
> Created at: 2020-12-20 05:51:51 UTC  
> Updated at: 2020-12-20 10:34:20 UTC  
> Closed at: 2020-12-20 09:19:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/103  

I create a simple C++ project to try Boost.Stacktrace.:  
*main.cpp*:  
```c++  
#include <iostream>  
  
#define BOOST_STACKTRACE_LINK  
#include <boost/stacktrace.hpp>  
  
void bar() { std::cout << boost::stacktrace::stacktrace(); }  
  
int main() { bar(); }  
```  
  
The project uses CMake to compile and link:  
```cmake  
cmake_minimum_required(VERSION 3.15)  
  
project(cpp_templates)  
  
find_package(Boost 1.75.0 REQUIRED COMPONENTS stacktrace_windbg_cached)  
  
if(Boost_FOUND)  
    include_directories(${Boost_INCLUDE_DIRS})  
    link_directories(${Boost_LIBRARY_DIRS})  
endif()  
  
if(MSVC)  
    add_compile_options(/std:c++17)  
endif()  
  
add_executable(c++_templates main.cpp)  
message(STATUS "Boost: ${Boost_LIBRARIES}")  
target_link_libraries(c++_templates ${Boost_LIBRARIES})  
```  
It will cause link error with Clang or MSVC:  
```log  
[build] [1/1 100% :: 0.181] Linking CXX executable src\c++_templates.exe  
[build] FAILED: src/c++_templates.exe   
[build] cmd.exe /C "cd . && C:\ProgramData\scoop\shims\clang++.exe -fuse-ld=lld-link -nostartfiles -nostdlib -g -Xclang -gcodeview -O0 -D_DEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrtd  src/CMakeFiles/c++_templates.dir/main.cpp.obj -o src\c++_templates.exe -Xlinker /implib:src\c++_templates.lib -Xlinker /pdb:src\c++_templates.pdb -Xlinker /version:0.0  -LC:/Boost/lib C:/Boost/lib/libboost_filesystem-clang11-mt-d-x64-1_75.lib  C:/Boost/lib/libboost_date_time-clang11-mt-d-x64-1_75.lib  C:/Boost/lib/libboost_stacktrace_windbg_cached-clang11-mt-d-x64-1_75.lib  -lbcrypt.lib  -lDbgeng.lib  -lole32.lib  -lDbgeng.lib  -lole32.lib  -lkernel32 -luser32 -lgdi32 -lwinspool -lshell32 -lole32 -loleaut32 -luuid -lcomdlg32 -ladvapi32 -loldnames && cd ."  
[build] lld-link: error: /failifmismatch: mismatch detected for '_ITERATOR_DEBUG_LEVEL':  
[build] >>> src/CMakeFiles/c++_templates.dir/main.cpp.obj has value 2  
[build] >>> libboost_stacktrace_windbg_cached-clang11-mt-d-x64-1_75.lib(windbg_cached.obj) has value 0  
[build] clang++: error: linker command failed with exit code 1 (use -v to see invocation)  
[build] ninja: build stopped: subcommand failed.  
```  
  
The mismatch `_ITERATOR_DEBUG_LEVEL` leads the failure. The meaning of `_ITERATOR_DEBUG_LEVEL` is:  
```  
_ITERATOR_DEBUG_LEVEL = 0 (in release mode)  
_ITERATOR_DEBUG_LEVEL = 1 (in release mode if _SECURE_SCL is defined)  
_ITERATOR_DEBUG_LEVEL = 2 (in debug mode)  
```  
Why the debug version of library of Boost.Stacktrace, which is ` libboost_stacktrace_windbg_cached-clang11-mt-d-x64-1_75.lib`, has the value of 0 with `_ITERATOR_DEBUG_LEVEL`? Is there anything wrong with my configure and build with `Boost`? I use the command to configure and build with `Boost` 1.75.0:  
```console  
b2  install --build-type=complete toolset=clang  
```  
and   
```console  
b2  install --build-type=complete toolset=msvc  
```  
Thanks very much.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-20 09:19:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/103#issuecomment-748582943  

AFAIK `b2` does not define the `_ITERATOR_DEBUG_LEVEL` by default. You can define it with something like this `b2  install toolset=clang define=_ITERATOR_DEBUG_LEVEL=1`. If you want the `_ITERATOR_DEBUG_LEVEL` to be set by default, please report an issue in this repo https://github.com/boostorg/build/ and provide motivation.  
  
This is not a stacktrace issue, closing.

---

## Comment 2

> Username: tan-wei  
> Created at: 2020-12-20 10:34:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/103#issuecomment-748589905  

Maybe I find the reason:  
  
`BOOST_STACKTRACE_USE_WINDBG` should be predefined instead of `BOOST_STACKTRACE_LINK`.  
  
BTW, thanks for your reply.
