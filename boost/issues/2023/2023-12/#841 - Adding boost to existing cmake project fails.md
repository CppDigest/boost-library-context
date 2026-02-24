# #841 - Adding boost to existing cmake project fails [Closed]

> Username: MasterBe  
> Created at: 2023-12-30 21:32:47 UTC  
> Updated at: 2024-04-28 12:39:40 UTC  
> Closed at: 2024-04-28 12:39:40 UTC  
> Url: https://github.com/boostorg/boost/issues/841  

Hello,  
  
I am having an issue using boost, I get the error: "**fatal error C1083: Cannot open include file: 'boost/algorithm/string.hpp': No such file or directory**". I got here following the steps:  
1. git clone --recursive https://github.com/boostorg/boost.git  
2. bootstrap and then .\b2  
3. added boost includes and linked the libs in cmake lists as below:  
target_include_directories(${PROJECT_NAME} INTERFACE external/boost/libs/algorithm/include)  
target_link_directories(${PROJECT_NAME} INTERFACE external/boost/libs)  
4. Then trying to use the boost string algos: #include <boost/algorithm/string.hpp>  
  
Could you please help?  
  
Thanks for reading!

---

## Comment 1

> Username: MasterBe  
> Created at: 2023-12-31 07:19:11 UTC  
> Url: https://github.com/boostorg/boost/issues/841#issuecomment-1872760374  

I tried using vcpkg and did the below in Cmakelists:  
# Find and include Boost  
find_package(Boost REQUIRED)  
message(STATUS "Boost_INCLUDE_DIRS: ${Boost_INCLUDE_DIRS}")  
message(STATUS "Boost_LIBRARIES: ${Boost_LIBRARIES}")  
# Include Boost headers  
target_include_directories(${PROJECT_NAME} INTERFACE ${Boost_INCLUDE_DIRS})  
# Link against Boost  
target_link_libraries(${PROJECT_NAME} INTERFACE Boost::headers)  
still I get the same issue: fatal error C1083: Cannot open include file: 'boost/algorithm/string.hpp': No such file or directory  
I am pretty sure I checked the include path and looked correct. Could please someone help?
