# #218 Update interface library properties [Open]

> Username: Caointean  
> Created at: 2025-07-17 22:04:37 UTC  
> Updated at: 2025-10-06 17:04:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/218  

Implement solution to problem from #217   
  
I used a glob as I usually do to keep the file list concise, but I don't know if it's the proper boost etiquette.  
The target_sources allow for wetter handling of the headers during install, but I also fixed the issue with the absolute path when using CMake version where the target_sources command with file sets is not available.  
  
You can test it with a sample project:  
```text  
 sample  
 ├── include  
 │   └── sample  
 │       └── header.hpp  
 ├── src  
 │   └── main.cpp  
 └── CMakeLists.txt  
```  
  
CMakeLists.txt:  
```cmake  
cmake_minimum_required(VERSION 3.28)  
  
project(sample VERSION 1.0.0)  
  
add_subdirectory(${PFR_DIR} pfr)  
  
add_executable(exe)  
target_sources(exe PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src/main.cpp)  
target_link_libraries(exe Boost::pfr)  
  
install(TARGETS exe EXPORT sampleTargets)  
  
add_library(lib INTERFACE)  
target_sources(lib INTERFACE FILE_SET HEADERS TYPE HEADERS BASE_DIRS ${CMAKE_CURRENT_SOURCE_DIR}/include/  
                                                           FILES "include/sample/header.hpp")  
target_link_libraries(lib INTERFACE Boost::pfr)  
  
install(TARGETS lib EXPORT sampleTargets FILE_SET HEADERS)  
install(TARGETS boost_pfr EXPORT sampleTargets FILE_SET HEADERS)  
  
install(EXPORT sampleTargets  
        FILE sampleTargets.cmake  
        NAMESPACE sample::  
        DESTINATION cmake/sample)  
  
```  
main.cpp:  
```cpp  
#include "boost/pfr.hpp"  
#include <iostream>  
  
struct A {  
    int _i1{1};  
    int _i2{2};  
    int _i3{3};  
    int _i4{4};  
};  
  
int main() {  
    boost::pfr::for_each_field(A{}, [](auto &v){ std::cout << v << "\n"; });  
    return 0;  
}  
  
```  
  
header.hpp:  
```cpp  
#pragma once  
  
#include "boost/pfr.hpp"  
  
// ... Whatever it's just a sample header...  
```

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-10-06 16:43:10 UTC  
> Url: https://github.com/boostorg/pfr/pull/218#issuecomment-3372765593  

@pdimov could you please take a look at this PR?  
  
Should it be fixed here or somewhere in the generic Boost's cmake wrappers?

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-10-06 17:04:40 UTC  
> Url: https://github.com/boostorg/pfr/pull/218#issuecomment-3372870229  

```  
        target_include_directories(boost_pfr INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>  
                                                       $<INSTALL_INTERFACE:include>)  
```  
is wrong for at least two reasons. First, `$<INSTALL_INTERFACE:include>` means `${CMAKE_INSTALL_PREFIX}/include`, which is not correct when CMAKE_INSTALL_INCLUDEDIR is not set to the default `include`. Second, when BOOST_INSTALL_LAYOUT is `versioned`, headers are installed in a subdirectory of CMAKE_INSTALL_INCLUDEDIR.  
  
I don't see any CMake install tests in your CI so I would recommend against touching this until you do, at least when your library is not part of the superproject (but when it isn't you still need to test that installation works).  
  
(Although of course this branch will only be exercised on CMake earlier than 3.23, so you need to make sure to test on at least one such.)  
  
As for using FILE_SET HEADERS instead of target_include_directories, we don't yet support this in the CMake infrastructure, but it's probably just a matter of adding the appropriate destination here  
  
https://github.com/boostorg/cmake/blob/9a834e122025a5a28b0d9d060cc311b2b0d02e70/include/BoostInstall.cmake#L300-L307  
  
although since CMAKE_INSTALL_INCLUDEDIR is the default (and BoostRoot adjusts this appropriately for versioned layout), it might even work without any changes.

---
