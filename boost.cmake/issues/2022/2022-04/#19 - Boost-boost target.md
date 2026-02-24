# #19 - Boost:boost target [Closed]

> Username: MichaelUnknown  
> Created at: 2022-04-03 21:16:00 UTC  
> Updated at: 2023-01-07 03:01:40 UTC  
> Closed at: 2023-01-07 03:01:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/19  

Hi,  
I use Boost via CMake FetchContent_declare (actually via [CPM.cmake](https://github.com/cpm-cmake/CPM.cmake) but it uses FetchContent internally). This works just fine, however I encountered a problem when I was using another dependency that was also depending on Boost itself. That dependency linked against `Boost:boost` (like `target_link_libraries(Dependency INTERFACE Boost::boost)`).  
  
The `Boost::boost` target is provided by Boost if it is installed as a systems library. On my ubuntu 20.04 the file `BoostConfig.cmake` contains the following code:  
```cmake  
# Compatibility targets  
  
if(NOT TARGET Boost::boost)  
  
  add_library(Boost::boost INTERFACE IMPORTED)  
  set_property(TARGET Boost::boost APPEND PROPERTY INTERFACE_LINK_LIBRARIES Boost::headers)  
  
  <some more code>  
  
endif()  
```  
However, with the new CMake integration, there is neither `Boost::boost` nor `Boost::headers`. Is there a plan to provide those targets with the new CMake integration? Or am I missing some configuration step for the targets to be available?

---

## Comment 1

> Username: ClausKlein  
> Created at: 2022-06-08 22:00:50 UTC  
> Updated at: 2022-06-08 22:06:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/19#issuecomment-1150458992  

Yes, this is really a Problem.  
I am trying to use a workaround for this: https://github.com/ClausKlein/boost-cmake/blob/87c5ddb42bcd6ab239cc48ca60b0f9c596ff98ed/cmake/CMakeLists.txt.in#L56  
  
and https://github.com/cpm-cmake/CPM.cmake/pull/359

---

## Comment 2

> Username: ClausKlein  
> Created at: 2022-06-08 22:08:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/19#issuecomment-1150464327  

And the `install` target has problems with **non existing directories**!  
see https://discourse.cmake.org/t/error-in-generated-cmake-install-cmake/5827/8

---

## Comment 3

> Username: bmagistro  
> Created at: 2022-12-24 02:39:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/19#issuecomment-1364445054  

I'm not sure how feasible it is, but could the boost cmake side simply maintain a list of header libraries and build up this target?  
  
The project I want to utilize this is a dependency of our main project.  It also leverages the `Boost_INCLUDE_DIRS` variable which made passing this in mostly painless (other than building the list the first time).  I fully realize the approach taken here is fragile and doesn't resolve dependencies automatically but it solves a problem for us.  
  
Code is reduced for brevity  
```  
set(BOOST_INCLUDE_LIBRARIES ...)  
FetchContent_Declare(...)  
FetchContent_MakeAvailable(...)  
  
foreach(__b_lib IN LISTS BOOST_INCLUDE_LIBRARIES)  
    list(APPEND _boost_incls "${boost_SOURCE_DIR}/libs/${__b_lib}/include")  
endforeach()  
  
set( Boost_INCLUDE_DIRS ${_boost_incls} CACHE PATH "Override" FORCE )  
```

---

## Comment 4

> Username: MichaelUnknown  
> Created at: 2022-12-26 22:49:08 UTC  
> Updated at: 2022-12-26 22:50:18 UTC  
> Url: https://github.com/boostorg/cmake/issues/19#issuecomment-1365494307  

@ClausKlein @bmagistro if you are interested, here is the workaround I use (with CPM.cmake). It gathers all the include directories from the directory structure (code could be cleaned up a bit).  
With `Boost:boost` and `Boost:headers` being just a collection of headers, I believe this should work for legacy usages of those targets. For a minimal but working example (depending on CrowCpp which depends on `Boost::asio`) see here: https://github.com/MichaelUnknown/cpp-docker-ci-cd.  
  
I think boostorg/cmake could also provide this target when all modules are present.  
  
```cmake  
# we want Boost 1.64 for asio  
find_package(Boost 1.64 QUIET)  
if(NOT Boost_FOUND)  
  # If not provided as system lib, use CPM.cmake to get Boost from the official repo  
  message(STATUS "XXX: Boost system lib NOT found")  
  CPMAddPackage(  
    NAME Boost  
    GITHUB_REPOSITORY boostorg/boost  
    GIT_TAG boost-1.81.0  
    VERSION 1.81.0  
  )  
  
  # Ugly workaround:  
  # Boost cmake support is still experimental, the Boost::headers / Boost::boost target  
  # is not provided if downloaded via FetchContent_declare / CPM.cmake  
  # so we add it manually  
  
  if(NOT TARGET Boost::headers)  
    message(STATUS "manually adding Boost::headers")  
  
    set(__boost_libs_dir "${Boost_SOURCE_DIR}/libs/")  
    file(GLOB __boost_all_components  
      LIST_DIRECTORIES true  
      RELATIVE "${__boost_libs_dir}"  
      "${__boost_libs_dir}/*/include")  
    file(GLOB __boost_all_components_num  
      LIST_DIRECTORIES true  
      RELATIVE "${__boost_libs_dir}"  
      "${__boost_libs_dir}/numeric/*/include")  
  
    foreach(__dir ${__boost_all_components})  
      set(__full_dir "${__boost_libs_dir}${__dir}")  
      #message(STATUS "full dir: ${__full_dir}")  
      list(APPEND __boost_all_includes ${__full_dir})  
    endforeach()  
    foreach(__dir ${__boost_all_components_num})  
      set(__full_dir "${__boost_libs_dir}${__dir}")  
      #message(STATUS "full num dir: ${__full_dir}")  
      list(APPEND __boost_all_includes ${__full_dir})  
    endforeach()  
  
    list(LENGTH __boost_all_includes __boost_all_includes_count)  
    message(STATUS "number of include dirs discovered: ${__boost_all_includes_count}")  
  
    # add target  
    add_library(Boost::headers INTERFACE IMPORTED)  
    set_target_properties(Boost::headers PROPERTIES INTERFACE_INCLUDE_DIRECTORIES "${__boost_all_includes}")  
    #get_target_property(xxx Boost::headers INTERFACE_INCLUDE_DIRECTORIES)  
    #foreach(xx ${xxx})  
    #  message(STATUS "Boost::headers includes: ${xx}")  
    #endforeach()  
  endif()  
  
  if(NOT TARGET Boost::boost)  
    message(STATUS "manually adding Boost::boost")  
    add_library(Boost::boost INTERFACE IMPORTED)  
    target_link_libraries(Boost::boost INTERFACE Boost::headers)  
  endif()  
else()  
  message(STATUS "XXX: Boost system lib found")  
endif()  
  
# use boost - other libs can use the targets as well  
add_executable(${PROJECT_NAME} ${sources})  
target_link_libraries(${PROJECT_NAME} PRIVATE Boost::boost Boost::asio)  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2023-01-04 20:18:58 UTC  
> Url: https://github.com/boostorg/cmake/issues/19#issuecomment-1371377524  

The develop branch now installs support for `find_package(Boost)` and the `Boost::boost` and `Boost::headers` targets.
