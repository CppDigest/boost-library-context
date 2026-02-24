# #328 - [cmake] Improve/fix FindHana.cmake for local builds [Closed]

> Username: tdauth  
> Created at: 2017-03-21 18:40:23 UTC  
> Updated at: 2017-04-02 18:09:15 UTC  
> Closed at: 2017-04-02 18:09:15 UTC  
> Url: https://github.com/boostorg/hana/issues/328  

I've followed the instruction from http://www.boost.org/doc/libs/1_61_0/libs/hana/doc/html/index.html#tutorial-installation  
  
downloaded the file FindHana.cmake. The file says I only have to specify Hana_INSTALL_PREFIX which is not true since it requires Hana_FIND_VERSION too when I want to build it locally.  
  
The custom build fails with:  
```  
CMake Error at /usr/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:148 (message):  
  Could NOT find Hana (missing: Hana_INCLUDE_DIR) (Required is at least  
  version "1.1.0")  
  
```  
  
Apparently it did not work to ignore the fact that it does not exist locally? Since it comes from these lines:  
```  
include(FindPackageHandleStandardArgs)  
find_package_handle_standard_args(Hana  
    REQUIRED_VARS Hana_INCLUDE_DIR  
    VERSION_VAR Hana_VERSION_STRING  
)  
```  
and not the later ones.  
  
This is my stuff in CMakeLists.txt:  
```  
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/")  
  
set(Hana_FIND_VERSION "1.1.0")  
set(Hana_INSTALL_PREFIX "${CMAKE_CURRENT_BINARY_DIR}/hana")  
find_package(Hana REQUIRED)  
include_directories(${Hana_INCLUDE_DIRS})  
```  
  
Commenting out the lines which require Hana_INCLUDE_DIR works. Maybe the file/documentation should also state that I need to use "add_dependencies(mytarget Hana)" to use the custom build successfully.

---

## Comment 1

> Username: pfultz2  
> Created at: 2017-03-21 19:03:00 UTC  
> Updated at: 2017-03-21 19:08:24 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-288185041  

Actually, Hana should provide a 'HanaConfig.cmake' so `find_package(Hana)` works without needing to modify the module path. This is the proper way to do this according to cmake documentation. From [Config-file Packages](https://cmake.org/cmake/help/v3.7/manual/cmake-packages.7.html#id11):  
  
> Config-file packages are provided by upstream vendors as part of development packages, that is, they belong with the header files and any other files provided to assist downstreams in using the package.  
  
And from [Find-module Packages](https://cmake.org/cmake/help/v3.7/manual/cmake-packages.7.html#id14)  
  
> A find module is a file with a set of rules for finding the required pieces of a dependency, primarily header files and libraries. Typically, a find module is needed when the upstream is not built with CMake, or is not CMake-aware enough to otherwise provide a package configuration file. Unlike a package configuration file, it is not shipped with upstream, but is used by downstream to find the files by guessing locations of files with platform-specific hints.  
  
Also, providing HanaConfig.cmake, will allow Hana to export its targets, so users can just do `target_link_libraries(myLib boost::hana)` and don't need to worry about variables such as `Hana_INCLUDE_DIRS` and such. You can use [bcm_boost_package](http://bcm.readthedocs.io/en/latest/src/BCMPackage.html#bcm-boost-package) function from [BCM](https://github.com/boost-cmake/bcm) to make this simpler:  
  
```  
bcm_boost_package(hana VERSION_HEADER include/boost/hana/version.hpp)  
```  
  
But if that doesn't fit your flow you can also use [bcm_install_targets](http://bcm.readthedocs.io/en/latest/src/BCMInstallTargets.html#bcm-install-targets) or do it manually.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-03-22 01:11:23 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-288268195  

@tdauth Thanks a lot for the report.  
  
Paul is right, Hana ought to provide a CMake config file instead. That being said, some kind of script will still be required to pull Hana and install it (after which the HanaConfig can be found), right Paul?

---

## Comment 3

> Username: pfultz2  
> Created at: 2017-03-22 02:59:59 UTC  
> Updated at: 2017-03-30 10:15:37 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-288284446  

Yes thats write. Cmake's `find_package` is for package configuration and not package management. So the user will still need to install hana either manually or automatically with [cget](https://github.com/pfultz2/cget)(with `cget install boostorg/hana`) or inside cmake with [`ExternalProject`](https://cmake.org/cmake/help/v3.2/module/ExternalProject.html):  
  
```  
ExternalProject_Add(boost_hana  
    URL https://github.com/boostorg/hana/archive/v1.1.0.tar.gz  
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/boost_hana  
    INSTALL_DIR <INSTALL_DIR>  
)  
```

---

## Comment 4

> Username: ldionne  
> Created at: 2017-03-30 05:11:54 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-290304247  

Alright, got it, thanks. I removed the `FindHana.cmake` header, which is buggy, and am providing a `HanaConfig` file instead.

---

## Comment 5

> Username: ldionne  
> Created at: 2017-03-31 05:07:11 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-290617008  

@tdauth Please LMK if that fixes your problem.

---

## Comment 6

> Username: ldionne  
> Created at: 2017-04-02 18:09:15 UTC  
> Url: https://github.com/boostorg/hana/issues/328#issuecomment-291003616  

Closing this, LMK if you still have trouble.
