# #44 - How to include the boost::asio header [Closed]

> Username: hungptit  
> Created at: 2023-11-15 21:21:01 UTC  
> Updated at: 2023-11-17 13:56:46 UTC  
> Closed at: 2023-11-17 13:56:46 UTC  
> Url: https://github.com/boostorg/cmake/issues/44  

I can use some boost libraries successfully using this pattern.  
  
```  
set(BOOST_INCLUDE_LIBRARIES json)  
set(BOOST_ENABLE_CMAKE ON)  
  
include(FetchContent)  
FetchContent_Declare(  
  Boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG boost-1.83.0  
  GIT_SHALLOW TRUE  
)  
  
FetchContent_MakeAvailable(Boost)  
  
# Compile some code against boost::json  
set(LIBNAME "smoketests_utilities")  
add_library(  
  ${LIBNAME} STATIC  
  address.cpp  
  client.cpp  
  server.cpp)  
target_link_libraries(${LIBNAME} PRIVATE address Boost::json)  
```  
  
The above pattern does not work for boost::asio. What is the right way to add header-only libraries to the include path?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-11-15 22:56:48 UTC  
> Url: https://github.com/boostorg/cmake/issues/44#issuecomment-1813397899  

You need to add `Boost::asio` to `target_link_libraries`.

---

## Comment 2

> Username: hungptit  
> Created at: 2023-11-17 13:56:46 UTC  
> Url: https://github.com/boostorg/cmake/issues/44#issuecomment-1816476904  

It works. Thank you, Peter.
