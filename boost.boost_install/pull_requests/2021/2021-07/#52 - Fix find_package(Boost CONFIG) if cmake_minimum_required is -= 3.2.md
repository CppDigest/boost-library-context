# #52 Fix find_package(Boost CONFIG) if cmake_minimum_required is <= 3.2 [Merged]

> Username: traversaro  
> Created at: 2021-07-17 16:16:29 UTC  
> Updated at: 2021-08-18 20:57:21 UTC  
> Merged at: 2021-08-17 13:43:48 UTC  
> Closed at: 2021-08-17 13:43:48 UTC  
> Url: https://github.com/boostorg/boost_install/pull/52  

With latest Boost and latest CMake, if one configure a project such as:  
~~~  
cmake_minimum_required(VERSION 3.2)  
  
project(boost_mre)  
  
find_package(Boost CONFIG REQUIRED)  
~~~  
  
the configuration fails with the following error:  
  
~~~  
(boost-cpp) straversaro@IITICUBLAP102:~/boost_find_package_mre/build$ cmake ..  
CMake Warning (dev) at /home/straversaro/mambaforge/envs/boost-cpp/lib/cmake/Boost-1.76.0/BoostConfig.cmake:240 (if):  
  Policy CMP0057 is not set: Support new IN_LIST if() operator.  Run "cmake  
  --help-policy CMP0057" for policy details.  Use the cmake_policy command to  
  set the policy and suppress this warning.  
  
  IN_LIST will be interpreted as an operator when the policy is set to NEW.  
  Since the policy is not set the OLD behavior will be used.  
Call Stack (most recent call first):  
  CMakeLists.txt:6 (find_package)  
This warning is for project developers.  Use -Wno-dev to suppress it.  
  
CMake Error at /home/straversaro/mambaforge/envs/boost-cpp/lib/cmake/Boost-1.76.0/BoostConfig.cmake:240 (if):  
  if given arguments:  
  
    "ALL" "IN_LIST" "Boost_FIND_COMPONENTS"  
  
  Unknown arguments specified  
Call Stack (most recent call first):  
  CMakeLists.txt:6 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/straversaro/boost_find_package_mre/build/CMakeFiles/CMakeOutput.log".  
~~~  
  
This happens because the `IN_LIST` operator is only available if [`CMP0057`](https://cmake.org/cmake/help/latest/policy/CMP0057.html) is enabled. This happens in most cases, either because the project set its `cmake_minimum_required` to a version higher then 3.3, or because FindBoost.cmake sets `CMP0057` to ON, but if one uses cmake_minumim_required <= 3.2 and `find_package(Boost CONFIG)`, the configuration fails. The problem occurs rarely, but sometimes it happens, as you can see in https://stackoverflow.com/questions/62930429/c-avro-cmake-failed  and https://sourceforge.net/p/ifcopenshell/discussion/1782716/thread/2758d87bcf/?limit=25  .  
  
This PR fixes this problem by enabling CMP0057 for just the time necessary to use the `IN_LIST` operator, and restoring the original project policies right after that.

---

## Comment 1

> Username: traversaro  
> Created_at: 2021-07-17 16:16:49 UTC  
> Url: https://github.com/boostorg/boost_install/pull/52#issuecomment-881921449  

Downstream issue: https://github.com/conda-forge/boost-cpp-feedstock/issues/103 .

---

## Comment 2

> Username: traversaro  
> Created_at: 2021-08-18 20:57:20 UTC  
> Url: https://github.com/boostorg/boost_install/pull/52#issuecomment-901425300  

Thanks @pdimov !

---
