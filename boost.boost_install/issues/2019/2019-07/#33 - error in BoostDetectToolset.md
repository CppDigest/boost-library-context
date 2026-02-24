# #33 - error in  BoostDetectToolset [Closed]

> Username: ZR233  
> Created at: 2019-07-05 05:51:26 UTC  
> Updated at: 2020-05-06 19:22:23 UTC  
> Closed at: 2020-05-06 19:22:23 UTC  
> Url: https://github.com/boostorg/boost_install/issues/33  

when using cmake find_package, the output is:  
`-- Boost toolset is mgw8 (GNU 8.1.0)`  
`--   Including D:/Boost/lib/cmake/boost_system-1.70.0/libboost_system-variant-mgw81-mt-d-x64-1_70-static.cmake`  
`--   ... skipped libboost_system-mgw81-mt-d-x64-1_70.a (mgw81, detected mgw8, set Boost_COMPILER to override)`  
`CMake Error at D:/Boost/lib/cmake/Boost-1.70.0/BoostConfig.cmake:95 (find_package):`  
`  Found package configuration file:`  
  
`    D:/Boost/lib/cmake/boost_system-1.70.0/boost_system-config.cmake`  
  
  
I found:  
  
`elseif(MINGW)`  
  
`  set(BOOST_DETECTED_TOOLSET "mgw")`  
  
`  if(_BOOST_COMPILER_VERSION_MAJOR GREATER 4)`  
`    set(_BOOST_COMPILER_VERSION_MINOR)`  
`  endif()`  
  
in BoostDetectToolset-1.70.0.cmake ，this led to errors.

---

## Comment 1

> Username: iliis  
> Created at: 2019-07-17 16:17:34 UTC  
> Url: https://github.com/boostorg/boost_install/issues/33#issuecomment-512351297  

I've stumpled across a very similar issue with clang. Cmake detects `Boost toolset is clang8 (Clang 8.0)`, but then fails to include my library that was built with `clang` instead of `clang8`:  
  
    ... skipped libboost_filesystem-clang-mt-a64-1_70.a (clang, detected clang8, set Boost_COMPILER to override)

---

## Comment 2

> Username: iliis  
> Created at: 2019-07-19 09:43:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/33#issuecomment-513166466  

https://github.com/boostorg/boost_install/commit/9967d60ec1a6eaa8a4781f61f87d621a89b605a9 fixes the logic around `Boost_COMPILER`, so you can at least work around this issue by manually setting the compiler identification string (e.g. set `-DBoost_COMPILER=mgw81` in your case)

---

## Comment 3

> Username: ashwinpn  
> Created at: 2020-02-27 02:36:04 UTC  
> Url: https://github.com/boostorg/boost_install/issues/33#issuecomment-591748416  

I have had this issue before, you could try to set the compiler identification string manually, it worked for me.

---

## Comment 4

> Username: pdimov  
> Created at: 2020-03-01 17:54:24 UTC  
> Url: https://github.com/boostorg/boost_install/issues/33#issuecomment-593125090  

This was an error in Boost.Build; `mgw8` was actually correct, and `mgw81` was wrong. It should be fixed in the next release by https://github.com/boostorg/build/pull/524. For now, setting Boost_COMPILER manually should work around the issue.
