# #276 - boost-1.71.0 problem with python [Open]

> Username: ghost  
> Created at: 2019-10-29 00:08:42 UTC  
> Updated at: 2020-04-25 23:09:20 UTC  
> Url: https://github.com/boostorg/python/issues/276  

Hi.I am trying to use boost 1.71 and I have a problem while 'cmak'ing a package. Can you help me for fixing this problem?  
  
Thank You  
  
```  
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.1")   
-- The CXX compiler identification is GNU 5.4.0  
-- Check for working CXX compiler: /usr/bin/c++  
-- Check for working CXX compiler: /usr/bin/c++ -- works  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Warning at cmake/base.cmake:198 (PROJECT):  
  DESCRIPTION keyword not followed by a value or was followed by a value that  
  expanded to nothing.  
Call Stack (most recent call first):  
  CMakeLists.txt:38 (setup_project)  
  
  
-- Performing Test R-pedantic  
-- Performing Test R-pedantic - Success  
-- Performing Test R-Wno-long-long  
-- Performing Test R-Wno-long-long - Success  
-- Performing Test R-Wall  
-- Performing Test R-Wall - Success  
-- Performing Test R-Wextra  
-- Performing Test R-Wextra - Success  
-- Performing Test R-Wcast-align  
-- Performing Test R-Wcast-align - Success  
-- Performing Test R-Wcast-qual  
-- Performing Test R-Wcast-qual - Success  
-- Performing Test R-Wformat  
-- Performing Test R-Wformat - Success  
-- Performing Test R-Wwrite-strings  
-- Performing Test R-Wwrite-strings - Success  
-- Performing Test R-Wconversion  
-- Performing Test R-Wconversion - Success  
-- Found Doxygen: /usr/bin/doxygen (found version "1.8.11") found components: doxygen dot   
-- Found Python: /usr/lib/x86_64-linux-gnu/libpython3.5m.so (found version "3.5") found components: Development   
CMake Error at /usr/lib/cmake/Boost-1.71.0/BoostConfig.cmake:117 (find_package):  
  Found package configuration file:  
  
    /usr/lib/cmake/boost_python-1.71.0/boost_python-config.cmake  
  
  but it set boost_python_FOUND to FALSE so package "boost_python" is  
  considered to be NOT FOUND.  Reason given by package:  
  
  No suitable build variant has been found.  
  
  The following variants have been tried and rejected:  
  
  * libboost_python27.so.1.71.0 (2.7, Boost_PYTHON_VERSION=3.5)  
  
  * libboost_python27.a (2.7, Boost_PYTHON_VERSION=3.5)  
  
Call Stack (most recent call first):  
  /usr/lib/cmake/Boost-1.71.0/BoostConfig.cmake:182 (boost_find_component)  
  /usr/local/share/cmake-3.16/Modules/FindBoost.cmake:443 (find_package)  
  binding/python/CMakeLists.txt:23 (find_package)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/mohammad/catkin_ws/lipm/copra/build/CMakeFiles/CMakeOutput.log".  
  
```

---

## Comment 1

> Username: ilovelinux  
> Created at: 2019-12-30 21:59:12 UTC  
> Url: https://github.com/boostorg/python/issues/276#issuecomment-569808528  

Same issue. Have you found a solution?

---

## Comment 2

> Username: Eleobert  
> Created at: 2020-04-25 23:07:12 UTC  
> Updated at: 2020-04-25 23:09:20 UTC  
> Url: https://github.com/boostorg/python/issues/276#issuecomment-619452457  

The same here too, trying to install it from the source code. My version is 1.72.0.
