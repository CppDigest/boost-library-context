# #188 - Ensure integration options are available [Closed]

> Username: alandefreitas  
> Created at: 2022-06-06 17:28:50 UTC  
> Updated at: 2022-08-03 00:25:26 UTC  
> Closed at: 2022-08-03 00:25:26 UTC  
> Url: https://github.com/boostorg/url/issues/188  

Ensure all integration options are working, document them, and include the integration tests recommended in boost-ci ([ref](https://github.com/boostorg/boost-ci/tree/master/test)).   
  
These options could be:  
  
- As a boost/libs subdirectory, with add_subdirectory(../..) or b2 (the default)  
- As add_subdirectory(url) after add_subdirectory(...) for each boost dependency (as in boost-ci)  
- As find_package(boost_url REQUIRED) (as in boost-ci)  
- As add_subdirectory(url) after find_package(Boost) (Most common in CMake)  
- As FetchContent / MakeAvailable (Almost the same as above)  
- As a vcpkg recipe (?)  
- A single-header version (?)
