# #61 - BoostConfig creates targets which are not available when using boost from subdirectory [Closed]

> Username: vasil-pashov  
> Created at: 2024-05-11 21:09:01 UTC  
> Updated at: 2024-05-16 07:36:11 UTC  
> Closed at: 2024-05-16 07:36:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/61  

## Summary  
  
BoostConfig creates additional "Compatibility targets" [here](https://github.com/boostorg/cmake/blob/e5b13bf36b7c46d657657e4ff59323a7e1182cdd/config/BoostConfig.cmake#L134). These targets, however, are not created if Boost is added as a subdirectory to an existing project, which leads to link errors.  
  
## Context  
I'm trying to build [OpenVDB](https://github.com/AcademySoftwareFoundation/openvdb) with Boost as a dependency. I'm using Cmake's `FetchContent` with `OVERRIDE_FIND_PACKAGE` enabled. OpenVDB uses `find_package` to find Boost and links to [Boost::disable_autolinking](https://github.com/AcademySoftwareFoundation/openvdb/blob/e1ff3e3db8e13d46e6a23441c77fd986fe66ce31/openvdb/openvdb/CMakeLists.txt#L261C9-L261C35) which is not created when Boost is used as subdirectory.  
  
<details>  
<summary> Repro </summary>  
  
```cmake  
# CMakeLists.txt  
cmake_minimum_required(VERSION 3.29.2)  
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)  
  
project(my_project)  
include(FetchContent)  
  
set(BUILD_EXAMPLES OFF)  
FetchContent_Declare(  
	zlib  
	URL https://github.com/madler/zlib/releases/download/v1.3.1/zlib131.zip  
	OVERRIDE_FIND_PACKAGE  
)  
  
set(BUILD_TESTS OFF)  
set(BUILD_FUZZERS OFF)  
set(BUILD_BENCHMARKS OFF)  
set(BUILD_EXAMPLES OFF)  
FetchContent_Declare(  
	blosc  
	URL https://github.com/Blosc/c-blosc/archive/refs/tags/v1.21.5.zip  
	OVERRIDE_FIND_PACKAGE  
)  
  
set(TBB_TEST OFF)  
set(TBB_INSTALL OFF)  
FetchContent_Declare(  
	tbb  
	GIT_REPOSITORY https://github.com/oneapi-src/oneTBB  
	GIT_TAG v2021.13.0-rc1  
	OVERRIDE_FIND_PACKAGE  
)  
  
FetchContent_Declare(  
	boost  
	URL https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.tar.xz  
	URL_HASH SHA256=2e64e5d79a738d0fa6fb546c6e5c2bd28f88d268a2a080546f74e5ff98f29d0e  
	OVERRIDE_FIND_PACKAGE  
)  
  
FetchContent_Declare(  
	openvdb  
	URL https://github.com/AcademySoftwareFoundation/openvdb/archive/refs/tags/v11.0.0.zip  
	OVERRIDE_FIND_PACKAGE  
)  
  
FetchContent_MakeAvailable(zlib blosc tbb boost openvdb)  
  
add_library(Blosc::blosc ALIAS blosc_shared)  
add_library(ZLIB::ZLIB ALIAS zlib)  
  
add_executable(my_project main.cpp)  
```  
  
</details>  
  
## Current workaround  
I'm adding a `boost-extra.cmake` file which contains a copy of the code creating the target.  
<details>  
<summary>Code</summary>  
  
```cmake  
FetchContent_Declare(  
	boost  
	URL https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.tar.xz  
	URL_HASH SHA256=2e64e5d79a738d0fa6fb546c6e5c2bd28f88d268a2a080546f74e5ff98f29d0e  
	OVERRIDE_FIND_PACKAGE  
)  
file(WRITE ${CMAKE_FIND_PACKAGE_REDIRECTS_DIR}/boost-extra.cmake  
[=[  
  add_library(Boost::boost INTERFACE IMPORTED)  
  set_property(TARGET Boost::boost APPEND PROPERTY INTERFACE_LINK_LIBRARIES Boost::headers)  
  
  add_library(Boost::diagnostic_definitions INTERFACE IMPORTED)  
  add_library(Boost::disable_autolinking INTERFACE IMPORTED)  
  add_library(Boost::dynamic_linking INTERFACE IMPORTED)  
  
  if(WIN32)  
  
    set_property(TARGET Boost::diagnostic_definitions PROPERTY INTERFACE_COMPILE_DEFINITIONS "BOOST_LIB_DIAGNOSTIC")  
    set_property(TARGET Boost::disable_autolinking PROPERTY INTERFACE_COMPILE_DEFINITIONS "BOOST_ALL_NO_LIB")  
    set_property(TARGET Boost::dynamic_linking PROPERTY INTERFACE_COMPILE_DEFINITIONS "BOOST_ALL_DYN_LINK")  
  
  endif()  
]=]  
)  
```  
  
</details>  
I think this pollutes the project and is not much future-proof.  
  
## Desired solution  
Building from a subdirectory should export the same targets as installing Boost and using `find_package`.  
  
## Environment Details  
Boost version: 1.85  
OS: Windows 11  
CMake version: 3.29.2

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-12 19:40:52 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2106354650  

It's easy enough to add `Boost::diagnostic_definitions`, `Boost::disable_autolinking`, and `Boost::dynamic_linking`, but `Boost::headers` doesn't do anything from a subdirectory.

---

## Comment 2

> Username: vasil-pashov  
> Created at: 2024-05-12 20:49:16 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2106370792  

Adding `Boost::diagnostic_definitions`, `Boost::disable_autolinking`, and `Boost::dynamic_linking` would solve my issue.  
  
Regarding `Boost::headers` isn't it just an alias for `Boost::boost` at least that's what the [docs](https://cmake.org/cmake/help/latest/module/FindBoost.html#imported-targets) say about it. I think it would be nice to have it as well to cover something like this:  
```cmake  
# Top level CMake  
FetchContent_Declare(  
boost  
GIT_REPOSITORY ...  
OVERRIDE_FIND_PACKAGTE  
)  
```  
  
```cmake  
# Some subdir or another project fetched via FetchContent  
find_package(boost REQUIRED)  
target_link_libraries(proj PIRVATE Boost::headers)  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-12 23:42:44 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2106413054  

`Boost::headers` and `Boost::boost` are the targets for header-only libraries. When Boost is installed, all headers go into the same directory, so one only needs a single include path, which these two targets provide.  
  
When using Boost as a subdirectory, though, the library headers are in their respective library directories, so from CMake you have to link to the library targets even when they are header-only.

---

## Comment 4

> Username: vasil-pashov  
> Created at: 2024-05-13 07:32:07 UTC  
> Updated at: 2024-05-13 07:32:17 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2106852034  

Ah, you're right! I've stumbled upon this yesterday and it made me scratch my head. Now I get it. Thanks! In this case do you think it's reasonable to have something like this?  
```cmake  
# If boost is a subproject  
if(NOT "^${CMAKE_SOURCE_DIR}$" STREQUAL "^${PROJECT_SOURCE_DIR}$")  
  add_library(Boost::headers INTERFACE)  
  target_include_directories(Boost::headers INTERFACE lib1_headers lib2_headers ...)  
endif()  
```

---

## Comment 5

> Username: pdimov  
> Created at: 2024-05-13 14:35:34 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2107781003  

Maybe, although that's going to add something like 150 include paths, not sure how reasonable that's going to be in practice. :-)

---

## Comment 6

> Username: vasil-pashov  
> Created at: 2024-05-14 14:30:20 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2110401189  

Yeah, you have a point. In a perfect world find_package and using it from source should behave exactly the same. I'm personally willing to have have large list of include directories (until it makes me hurt) but I understand that you have to think about all other users. Maybe hiding the target behind some flag would be worth it... Anyway having `Boost::diagnostic_definitions`, `Boost::disable_autolinking`, and `Boost::dynamic_linking` would be nice.

---

## Comment 7

> Username: pdimov  
> Created at: 2024-05-14 23:40:47 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2111332218  

OK, I added support for BOOST_ENABLE_COMPATIBILITY_TARGETS=ON to `develop`.

---

## Comment 8

> Username: vasil-pashov  
> Created at: 2024-05-16 07:36:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/61#issuecomment-2114283073  

Awesome, thanks. I'm closing this.
