# #810 - Boost.compute no longer a self contained library? [Open]

> Username: rosenrodt  
> Created at: 2019-01-10 09:22:08 UTC  
> Updated at: 2019-01-10 09:22:08 UTC  
> Url: https://github.com/boostorg/compute/issues/810  

In the official Boost.Compute [documentation](https://www.boost.org/doc/libs/1_69_0/libs/compute/doc/html/boost_compute/getting_started.html#boost_compute.getting_started.compilation_and_usage), the tutorial starts with how to get Boost.Compute running as succinctly as:  
  
`g++ -I/path/to/compute/include main.cpp -lOpenCL`  
  
But it won't work.  
  
It seems we also need to add Boost in the include path, as many source files need Boost library features such as `BOOST_STATIC_ASSERT()`. So maybe the tutorial can be updated to clear up some confusions  
  
The tutorial also implies no external dependencies are needed. I need to define macro `BOOST_COMPUTE_NO_BOOST_CHRONO` to disable linking with `libboost_chrono`. Again it seems to go undocumented too  
  
For reference, here is my cmake script for compiling `example/hello_world.cpp` to make it compile without external dependencies.   
  
```cmake  
find_path(BoostCompute_INCLUDE_DIRS   
  NAME boost/compute.hpp  
  PATHS /repo/boost.compute/include)  
find_package(OpenCL REQUIRED)  
find_package(Boost 1.54 REQUIRED)  
  
add_executable(compute_hello_world hello_world.cpp)  
target_include_directories(compute_hello_world PRIVATE   
  ${BoostCompute_INCLUDE_DIRS}  
  ${OpenCL_INCLUDE_DIRS}  
  ${Boost_INCLUDE_DIRS}  
  )  
target_link_libraries(compute_hello_world   
  ${OpenCL_LIBRARIES}  
  )  
target_compile_definitions(compute_hello_world PRIVATE   
  BOOST_COMPUTE_NO_BOOST_CHRONO # disable linking with boost chrono library  
  )  
```
