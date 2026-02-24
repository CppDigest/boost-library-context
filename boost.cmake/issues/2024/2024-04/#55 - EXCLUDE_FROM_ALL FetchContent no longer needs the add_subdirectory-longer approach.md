# #55 - EXCLUDE_FROM_ALL FetchContent no longer needs the add_subdirectory/longer approach [Closed]

> Username: pratikpc  
> Created at: 2024-04-01 18:52:21 UTC  
> Updated at: 2024-04-08 11:10:04 UTC  
> Closed at: 2024-04-08 11:10:04 UTC  
> Url: https://github.com/boostorg/cmake/issues/55  

In Boost Example code provided  
  
```cmake  
FetchContent_GetProperties(Boost)  
  
if(NOT Boost_POPULATED)  
  
  message(STATUS "Fetching Boost")  
  FetchContent_Populate(Boost)  
  
  message(STATUS "Configuring Boost")  
  add_subdirectory(${Boost_SOURCE_DIR} ${Boost_BINARY_DIR} EXCLUDE_FROM_ALL)  
  
endif()  
```  
  
This has been done to add support for EXCLUDE_FROM_ALL  
  
However, [from CMake version 3.28, the same is no longer needed](https://cmake.org/cmake/help/latest/module/FetchContent.html)  
  
![image](https://github.com/boostorg/cmake/assets/28534575/28fed32c-34bf-422f-b2fd-f5e6f8c616a8)  
  
  
For most users, it would probably be far easier to write  
  
```cmake  
  
FetchContent_Declare(  
  Boost  
  URL https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.tar.xz  
  URL_MD5 893b5203b862eb9bbd08553e24ff146a  
  DOWNLOAD_EXTRACT_TIMESTAMP ON  
  EXCLUDE_FROM_ALL  
)  
set(BOOST_INCLUDE_LIBRARIES timer filesystem regex)  
FetchContent_MakeAvailable(Boost)  
```  
  
For those with newer versions of CMake would it be okay if I create a PR for adding the same?    
As an alternative of course.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-01 22:56:06 UTC  
> Url: https://github.com/boostorg/cmake/issues/55#issuecomment-2030724154  

I wouldn't mind a PR along those lines.
