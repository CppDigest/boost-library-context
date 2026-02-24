# #10 - Is CMake support for Boost.Array still experimental? [Closed]

> Username: mloskot  
> Created at: 2020-03-17 22:42:06 UTC  
> Updated at: 2023-09-08 07:02:51 UTC  
> Closed at: 2023-09-08 07:02:51 UTC  
> Url: https://github.com/boostorg/array/issues/10  

Is this note up to date?  
  
https://github.com/boostorg/array/blob/e5f4af3dd212608f6a4a639a82f3484234807eec/CMakeLists.txt#L5-L6  
  
`CMakeLists.txt` have been added across many if not most libraries now, and Boost.Array seems to be missing the following  
  
1. `project(boost_array VERSION "${BOOST_SUPERPROJECT_VERSION}" LANGUAGES CXX)`  
  
2. Install  
  
```  
# boost_install requires PROJECT_VERSION  
# Without the superproject, we don't have any, so skip installation  
if(BOOST_SUPERPROJECT_VERSION)  
  include(BoostInstall)  
  boost_install(TARGETS boost_assert HEADER_DIRECTORY include/)  
endif()  
```  
  
3. Tests  
  
```  
# BUILD_TESTING is the standard CTest variable that enables testing  
if(BUILD_TESTING)  
  add_subdirectory(test)  
endif()  
```  
  
Question is if these are applicable or perhaps Boost.Array has a different plan.

---

## Comment 1

> Username: vardhanyadav1714  
> Created at: 2021-01-18 16:50:19 UTC  
> Url: https://github.com/boostorg/array/issues/10#issuecomment-762365216  

Bro can you tell me more about it i wanna make my first contribution
