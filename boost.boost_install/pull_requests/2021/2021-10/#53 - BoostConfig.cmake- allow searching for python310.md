# #53 BoostConfig.cmake: allow searching for python310 [Merged]

> Username: shr-project  
> Created at: 2021-10-19 12:28:45 UTC  
> Updated at: 2021-10-19 14:56:41 UTC  
> Merged at: 2021-10-19 14:56:31 UTC  
> Closed at: 2021-10-19 14:56:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/53  

* accept double digits in Python3_VERSION_MINOR  
  
* if someone is using e.g.:  
  find_package(Python3 REQUIRED)  
  find_package(Boost REQUIRED python${Python3_VERSION_MAJOR}${Python3_VERSION_MINOR})  
  
  with python-3.10 then it currently fails with:  
  
  -- Found PythonLibs: /usr/lib/libpython3.10.so (found version "3.10.0")  
  -- Found Python3: -native/usr/bin/python3-native/python3 (found version "3.10.0") found components: Interpreter  
  CMake Error at /usr/lib/cmake/Boost-1.77.0/BoostConfig.cmake:141 (find_package):  
    Could not find a package configuration file provided by "boost_python310"  
    (requested version 1.77.0) with any of the following names:  
  
      boost_python310Config.cmake  
      boost_python310-config.cmake  
  
    Add the installation prefix of "boost_python310" to CMAKE_PREFIX_PATH or  
    set "boost_python310_DIR" to a directory containing one of the above files.  
    If "boost_python310" provides a separate development package or SDK, be  
    sure it has been installed.  
  Call Stack (most recent call first):  
    /usr/lib/cmake/Boost-1.77.0/BoostConfig.cmake:258 (boost_find_component)  
    /usr/share/cmake-3.21/Modules/FindBoost.cmake:594 (find_package)  
    CMakeLists.txt:18 (find_package)  
  
Signed-off-by: Martin Jansa <martin.jansa@lge.com>

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-10-19 14:56:41 UTC  
> Url: https://github.com/boostorg/boost_install/pull/53#issuecomment-946809818  

Thanks!

---
