# #1128 - "CMake Error (BoostRoot): CMake support in Boost is experimental [..] Please use b2" - have I been following an entirely wrong path? [Open]

> Username: aral-matrix  
> Created at: 2026-02-22 12:26:08 UTC  
> Updated at: 2026-02-22 12:26:08 UTC  
> Url: https://github.com/boostorg/boost/issues/1128  

Related: https://github.com/boostorg/nowide/issues/202  
  
As my question is more generic and about the CMake configuration of the boost super-project, I wanted to ask it here:  
  
I tried via the following CMake FetchContent call to pull in only nowide (the suggestion to use tools/cmake came from the horrible slop machine, that I really don't want to use but I am desperate enough to see if it produces any useful ideas):  
```  
include(FetchContent)  
FetchContent_Declare(  
    boost_nowide  
    GIT_REPOSITORY https://github.com/boostorg/boost.git  
    GIT_TAG        boost-1.73.0  
    GIT_SUBMODULES libs/nowide;libs/config;libs/smart_ptr;tools/cmake  
    GIT_SHALLOW    TRUE  
    # OVERRIDE_FIND_PACKAGE  
)  
message( NOTICE "AFTER FetchContent_Declare" )  
FetchContent_MakeAvailable(boost_nowide)  
```  
  
This results in a very clear error message:  
CMake Error at build/_deps/boost_nowide-src/tools/cmake/include/BoostRoot.cmake:13 (message):  
  CMake support in Boost is experimental and part of an ongoing development  
  effort.  **It's not ready for use yet**.  Please use b2 (Boost.Build) to build  
  and install Boost.  
  
Since this wasn't pointed out to me in the above linked ticket, I am wondering: Have I managed to trigger an outdated error message that I should disable with  
```  
set( BOOST_ENABLE_CMAKE ON )  
```  
or should I really not use CMake with Boost?  
  
If I enable cmake, I get tons of dependency errors and after pulling in a ridiculous amount of libraries:  
```  
    GIT_SUBMODULES libs/core;libs/nowide;libs/config;libs/smart_ptr;libs/assert;libs/static_assert;libs/move;libs/throw_exception;libs/type_traits;tools/cmake  
```  
and adding them all to the TARGETS to install / export, I get a ton of these errors:  
```  
CMake Error: install(EXPORT "boost_move-targets" ...) includes target "boost_move" which requires target "boost_assert" that is not in this export set, but in multiple other export sets: lib/cmake/boost_assert-1.73.0/boost_assert-targets.cmake, lib/cmake/mylib/mylibTargets.cmake.  
```  
  
In summary:  
1) should I abandon the idea of CMake working fine with Boost libraries?  
2) if so, is there a minimal example somewhere how to use Boost.Build from a CMake project?
