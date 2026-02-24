# #254 CMake: Fixup stand-alone build to not depend on removed system stub library [Open]

> Username: nigels-com  
> Created at: 2025-11-04 06:37:18 UTC  
> Updated at: 2026-02-06 01:13:24 UTC  
> Url: https://github.com/boostorg/cobalt/pull/254  

I had trouble building cobalt stand-alone using system-installed boost 1.89.0, apparently due to the   
[removal of Boost::system](https://github.com/boostorg/system/issues/132).  This changes uses the `Boost_INCLUDE_DIR` variable instead.  
  
Without this change:  
  
```  
$ CMAKE_PREFIX_PATH=/opt cmake .. -G Ninja -DCMAKE_CXX_COMPILER=g++-14 -DCMAKE_BUILD_TYPE=Debug  
CMake Error at /opt/boost-1.89.0/lib/cmake/Boost-1.89.0/BoostConfig.cmake:141 (find_package):  
  Could not find a package configuration file provided by "boost_system"  
  (requested version 1.89.0) with any of the following names:  
  
    boost_systemConfig.cmake  
    boost_system-config.cmake  
  
  Add the installation prefix of "boost_system" to CMAKE_PREFIX_PATH or set  
  "boost_system_DIR" to a directory containing one of the above files.  If  
  "boost_system" provides a separate development package or SDK, be sure it  
  has been installed.  
Call Stack (most recent call first):  
  /opt/boost-1.89.0/lib/cmake/Boost-1.89.0/BoostConfig.cmake:262 (boost_find_component)  
  /usr/share/cmake-3.28/Modules/FindBoost.cmake:594 (find_package)  
  CMakeLists.txt:178 (find_package)  
  ```

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-11-04 09:26:50 UTC  
> Url: https://github.com/boostorg/cobalt/pull/254#issuecomment-3484859395  

Why did you remove `context` ?

---

## Comment 2

> Username: nigels-com  
> Created_at: 2025-11-04 09:50:58 UTC  
> Url: https://github.com/boostorg/cobalt/pull/254#issuecomment-3484969401  

Oh that is not intended, will revise.

---

## Comment 3

> Username: nigels-com  
> Created_at: 2025-11-26 11:02:04 UTC  
> Url: https://github.com/boostorg/cobalt/pull/254#issuecomment-3580786003  

Ready for further review now.

---

## Comment 4

> Username: nigels-com  
> Created_at: 2026-02-06 01:13:24 UTC  
> Url: https://github.com/boostorg/cobalt/pull/254#issuecomment-3857266688  

Can the failing build be retried?

---
