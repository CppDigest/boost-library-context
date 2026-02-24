# #580 - Trying to build with CMake, but dependency is not maitained with add_subdirectory [Open]

> Username: obhi-d  
> Created at: 2021-11-09 21:32:19 UTC  
> Updated at: 2021-12-06 14:47:29 UTC  
> Url: https://github.com/boostorg/boost/issues/580  

Hi,  
  
I was trying to build boost with cmake, but I had several errors of the form:  
```  
CMake Error at libs/date_time/CMakeLists.txt:10 (add_library):  
    Target "boost_date_time" links to target "Boost::tuple" but the target was  
    not found.  Perhaps a find_package() call is missing for an IMPORTED  
    target, or an ALIAS target is missing?  
    
    
  CMake Error at libs/date_time/CMakeLists.txt:10 (add_library):  
    Target "boost_date_time" links to target "Boost::function" but the target  
    was not found.  Perhaps a find_package() call is missing for an IMPORTED  
    target, or an ALIAS target is missing?  
```  
Looking at BoostRoot.cmake it seems the for loop at line 224:  
```  
foreach(__boost_lib_cml IN LISTS __boost_libraries)  
...  
   add_subdirectory(libs/${__boost_lib})  
    __boost_auto_install(${__boost_lib})  
...  
  endif()  
```  
  
does not care about the order of dependencies while doing add_subdirectory. Wouldn't the correct order of add_subdirectory be in accordance with the order in which ${__boost_include_libraries} are listed instead of alphabetical order ?

---

## Comment 1

> Username: pvthuyet  
> Created at: 2021-12-06 14:40:46 UTC  
> Url: https://github.com/boostorg/boost/issues/580#issuecomment-986838286  

I got the same issues  
I was trying to use boost with cmake:  
  
```  
cmake_minimum_required(VERSION 3.15)  
  
include(FetchContent)  
  
set(BOOST_ENABLE_CMAKE ON)  
  
FetchContent_Declare(  
        boostorg  
        GIT_REPOSITORY https://github.com/boostorg/boost.git  
        GIT_TAG        "boost-1.76.0"  
)  
  
FetchContent_GetProperties(boostorg)  
if (NOT boostorg_POPULATED)  
    FetchContent_Populate(boostorg)  
    add_subdirectory(${boostorg_SOURCE_DIR} ${boostorg_BINARY_DIR})  
endif ()  
  
include_directories(${boostorg_SOURCE_DIR})  
```  
  
and errors:  
  
```  
Severity	Code	Description	Project	File	Line	Suppression State  
Error		CMake Error at out/build/x64-Debug/_deps/boostorg-src/libs/property_tree/test/CMakeLists.txt:17 (add_executable):  
  Target "test-xml-parser-rapidxml" links to target "Boost::multi_index" but  
  the target was not found.  Perhaps a find_package() call is missing for an  
  IMPORTED target, or an ALIAS target is missing?		D:\projects\toneworks\Composer\out/build/x64-Debug/_deps/boostorg-src/libs/property_tree/test/CMakeLists.txt	17	  
  
```  
Does anyone know how to fix it?

---

## Comment 2

> Username: obhi-d  
> Created at: 2021-12-06 14:45:41 UTC  
> Updated at: 2021-12-06 14:47:29 UTC  
> Url: https://github.com/boostorg/boost/issues/580#issuecomment-986842423  

Since officially CMake is not supported, I guess we still have to rely on b2 (so you will have to do a add_custom_target, with commands invoking b2).  
EDIT: Of course this is not ideal. I wanted to pass the toolchain file to the build, and hence a CMake build would have been very ideal.
