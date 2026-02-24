# #511 - BOOST_HAS_PTHREADS not set with emscripten [Open]

> Username: ernestum  
> Created at: 2025-03-11 23:52:29 UTC  
> Updated at: 2025-03-12 09:56:15 UTC  
> Url: https://github.com/boostorg/config/issues/511  

This looks like a regression on #402   
  
Minimal example to reproduce with cmake:  
  
```cmake  
  
cmake_minimum_required(VERSION 3.27)  
project(boost_wasm_test)  
  
set(CMAKE_CXX_STANDARD 20)  
  
include(FetchContent)  
  
FetchContent_Declare(  
        Boost  
        URL https://github.com/boostorg/boost/releases/download/boost-1.87.0/boost-1.87.0-cmake.7z  
        EXCLUDE_FROM_ALL  
)  
set(BOOST_INCLUDE_LIBRARIES geometry)  
FetchContent_MakeAvailable(Boost)  
  
  
add_executable(boost_wasm_test main.cpp)  
target_link_libraries(boost_wasm_test Boost::geometry)  
target_compile_options(boost_wasm_test PRIVATE -pthread)  
target_link_options(boost_wasm_test PRIVATE -pthread)  
  
```  
  
(main.cpp is just generic hello world)  
  
I get lots of errors like   
  
```  
In file included from /home/m/Documents/Linos/boost_wasm_test/build_emscripten/_deps/boost-src/libs/container/src/synchronized_pool_resource.cpp:14:  
/home/m/Documents/Linos/boost_wasm_test/build_emscripten/_deps/boost-src/libs/container/include/boost/container/detail/thread_mutex.hpp:109:12: error: '__declspec' attributes are not enabled; use '-fdeclspec' or '-fms-extensions' to enable support for __declspec attributes  
  109 | extern "C" __declspec(dllimport) void __stdcall InitializeCriticalSection(::_RTL_CRITICAL_SECTION *);  
```  
  
Which indicates, that BOOST_HAS_PTHREADS is not properly set.  
Or am I using CMake wrong?  
  
I am on emscripten v3.1.61  
Same result with latest emscripten 4.04

---

## Comment 1

> Username: ernestum  
> Created at: 2025-03-12 09:56:14 UTC  
> Url: https://github.com/boostorg/config/issues/511#issuecomment-2717304269  

Workaround (as described in #402) is to add   
  
```cmake  
if(EMSCRIPTEN)  
    add_definitions(-DBOOST_HAS_PTHREADS)  # a workaround to https://github.com/boostorg/config/issues/402  
endif()  
```  
BEFORE the FetchContent_Declare
