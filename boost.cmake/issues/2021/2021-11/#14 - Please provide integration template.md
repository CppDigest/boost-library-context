# #14 - Please provide integration template? [Closed]

> Username: swex  
> Created at: 2021-11-09 07:28:24 UTC  
> Updated at: 2021-12-13 14:41:57 UTC  
> Closed at: 2021-12-13 14:41:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/14  

1. What is the current state of boost cmake support?  
2. I'm trying to use boost with `FetchContent` have the following:  
```cmake  
set(Boost_NO_BOOST_CMAKE TRUE CACHE BOOL "Do not search system for Boost" FORCE)  
set(Boost_NO_SYSTEM_PATHS TRUE CACHE BOOL "Do not search system for Boost" FORCE)  
  
  
FetchContent_Declare(  
        boost  
        GIT_REPOSITORY https://github.com/boostorg/boost.git  
        GIT_TAG develop  
        GIT_SHALLOW TRUE  
)  
FetchContent_GetProperties(boost)  
if (NOT boost_POPULATED)  
    FetchContent_Populate(boost)  
    add_subdirectory(${boost_SOURCE_DIR} ${boost_BINARY_DIR})  
    set(Boost_INCLUDE_DIR ${boost_SOURCE_DIR}/libs/config/include/)  
endif ()  
  
include_directories(${boost_SOURCE_DIR})  
  
target_link_libraries(${LIB_NAME} PUBLIC Boost::asio)  
  
```  
As you can see I do `FetchContent_Populate`, `add_subdirectory` and set `Boost_INCLUDE_DIR`  by hand, otherwise cmake can't find  `version.hpp`. Instead of just using `FetchContent_MakeAvailable`.   
I seem to do something wrong, could you indicate what exactly?   
  
3. I also plan to use  `safe_numerics` in my project, but it seems that there is no cmake support for it now, but it was surprise for me that safe numerics includes was taken from system paths even that I put:  
```cmake  
set(Boost_NO_BOOST_CMAKE TRUE CACHE BOOL "Do not search system for Boost" FORCE)  
set(Boost_NO_SYSTEM_PATHS TRUE CACHE BOOL "Do not search system for Boost" FORCE)  
```  
on top of my root CMakeLists.txt. How to properly ignore system includes?

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-09 14:20:23 UTC  
> Url: https://github.com/boostorg/cmake/issues/14#issuecomment-964195089  

> set(Boost_NO_BOOST_CMAKE TRUE CACHE BOOL "Do not search system for  
> Boost" FORCE)  
> set(Boost_NO_SYSTEM_PATHS TRUE CACHE BOOL "Do not search system for  
> Boost" FORCE)  
  
These are FindBoost variables. They have no effect when using Boost with  
add_subdirectory.  
  
> FetchContent_Declare(  
>         boost  
>         GIT_REPOSITORY https://github.com/boostorg/boost.git  
>         GIT_TAG develop  
>         GIT_SHALLOW TRUE  
> )  
> FetchContent_GetProperties(boost)  
> if (NOT boost_POPULATED)  
>     FetchContent_Populate(boost)  
>     add_subdirectory(${boost_SOURCE_DIR} ${boost_BINARY_DIR})  
>     set(Boost_INCLUDE_DIR ${boost_SOURCE_DIR}/libs/config/include/)  
> endif ()  
>   
> include_directories(${boost_SOURCE_DIR})  
  
This is unnecessary.  
  
> target_link_libraries(${LIB_NAME} PUBLIC Boost::asio)  
>   
> As you can see I do FetchContent_Populate, add_subdirectory and set  
> Boost_INCLUDE_DIR by hand, otherwise cmake can't find version.hpp. Instead  
> of just using FetchContent_MakeAvailable.  
  
I don't know why. FetchContent_MakeAvailable should work.  
  
Boost_INCLUDE_DIR is also a FindBoost variable, and shouldn't be needed.  
  
> I seem to do something wrong, could you indicate what exactly?  
>   
> 3.	I also plan to use safe_numerics in my project, but it seems that there is  
> no cmake support for it now, but it was surprise for me that safe numerics  
> includes was taken from system paths even that I put:  
>   
> set(Boost_NO_BOOST_CMAKE TRUE CACHE BOOL "Do not search system for  
> Boost" FORCE)  
> set(Boost_NO_SYSTEM_PATHS TRUE CACHE BOOL "Do not search system for  
> Boost" FORCE)  
>   
> on top of my root CMakeLists.txt. How to properly ignore system includes?  
  
If you have Boost installed in a system location such as /usr/include, the  
compiler will find it. This happens outside CMake. I'm not sure whether  
there's any CMake variable that can help.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-11-09 16:56:09 UTC  
> Url: https://github.com/boostorg/cmake/issues/14#issuecomment-964343286  

I just tried it. My CMakeLists.txt is:  
```  
cmake_minimum_required(VERSION 3.14..3.20)  
  
project(test_fetch_asio LANGUAGES CXX)  
  
include(FetchContent)  
  
set(FETCHCONTENT_QUIET FALSE)  
  
FetchContent_Declare(  
  boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG develop  
  GIT_SHALLOW TRUE  
  GIT_PROGRESS TRUE  
  USES_TERMINAL_DOWNLOAD TRUE  
)  
  
set(BOOST_INCLUDE_LIBRARIES asio)  
  
FetchContent_MakeAvailable(boost)  
  
add_executable(main main.cpp)  
target_link_libraries(main Boost::asio)  
```  
and my `main.cpp` is  
```  
#include <boost/asio.hpp>  
  
int main()  
{  
}  
```  
It works for me. The line `set(BOOST_INCLUDE_LIBRARIES asio)` is not strictly necessary, but without it, `cmake --build .` builds the entire Boost, and Asio doesn't need most of it.

---

## Comment 3

> Username: swex  
> Created at: 2021-12-13 14:41:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/14#issuecomment-992547872  

Thank you! For it works now, with `boost-1.78.0` tag! Looks like was trying some WIP version before.
