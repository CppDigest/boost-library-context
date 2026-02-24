# #191 - cmake: Incorrect `__float128` detection [Closed]

> Username: iv-m  
> Created at: 2024-04-22 10:58:26 UTC  
> Updated at: 2024-04-23 10:41:20 UTC  
> Closed at: 2024-04-23 10:09:47 UTC  
> Url: https://github.com/boostorg/charconv/issues/191  

Current develop branch uses the following CMake code to detect presence of  `__float128` types: https://github.com/boostorg/charconv/blob/100473b1dc3c3dfff0513e584c8f447824c7e34c/CMakeLists.txt#L23  
  
But `check_cxx_source_compiles` function takes code as the first argument, and, as far as I cant tell, cannot read the source from file. So, it tries to compile `config/has_float128.cpp` as C++ code, and, of course, fails. I'm getting the following error in `CMakeFiles/CMakeConfigureLog.yaml`:  
  
```  
Run Build Command(s): /usr/bin/cmake -E env VERBOSE=1 /usr/bin/gmake -f Makefile cmTC_38738/fast  
/usr/bin/gmake  -f CMakeFiles/cmTC_38738.dir/build.make CMakeFiles/cmTC_38738.dir/build  
gmake[1]: Entering directory '/usr/src/boost/bld/CMakeFiles/CMakeScratch/TryCompile-oiusM4'  
Building CXX object CMakeFiles/cmTC_38738.dir/src.cxx.o  
/usr/bin/c++ -DQUADMATH_FOUND   -o CMakeFiles/cmTC_38738.dir/src.cxx.o -c /usr/src/boost/bld/CMakeFiles/CMakeScratch/TryCompile-oiusM4/src.cxx  
/usr/src/boost/bld/CMakeFiles/CMakeScratch/TryCompile-oiusM4/src.cxx:1:1: error: 'config' does not name a type  
    1 | config/has_float128.cpp  
      | ^~~~~~  
gmake[1]: *** [CMakeFiles/cmTC_38738.dir/build.make:78: CMakeFiles/cmTC_38738.dir/src.cxx.o] Error 1  
gmake[1]: Leaving directory '/usr/src/boost/bld/CMakeFiles/CMakeScratch/TryCompile-oiusM4'  
gmake: *** [Makefile:127: cmTC_38738/fast] Error 2  
```  
  
Indeed `/usr/src/boost/bld/CMakeFiles/CMakeScratch/TryCompile-oiusM4/src.cxx` contains `config/has_float128.cpp` literally.  
  
I'm using cmake 3.29.1.

---

## Comment 1

> Username: iv-m  
> Created at: 2024-04-22 11:16:23 UTC  
> Url: https://github.com/boostorg/charconv/issues/191#issuecomment-2069130953  

I think something like this should be better match for what is written in `Jamfile`  
  
```  
try_compile(QUADMATH_FOUND  
            SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/config/has_float128.cpp  
            LINK_LIBRARIES quadmath)  
```

---

## Comment 2

> Username: mborland  
> Created at: 2024-04-23 06:37:04 UTC  
> Url: https://github.com/boostorg/charconv/issues/191#issuecomment-2071512715  

I do like the `try_compile` option, but since it requires CMake 3.25 I think it's far too new for a C++11 library. I'll try pasting in the code from `config/has_float128.cpp`.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-04-23 09:56:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/191#issuecomment-2071897725  

`try_compile` doesn't require 3.25, only the newer signatures do.

---

## Comment 4

> Username: mborland  
> Created at: 2024-04-23 10:00:12 UTC  
> Url: https://github.com/boostorg/charconv/issues/191#issuecomment-2071905420  

> `try_compile` doesn't require 3.25, only the newer signatures do.  
  
My mistake. I still think `check_cxx_source_runs` is preferable here because systems like Alpine linux provide the header `<quadmath.h>` but not the compiled library. See: https://github.com/boostorg/charconv/issues/176

---

## Comment 5

> Username: pdimov  
> Created at: 2024-04-23 10:41:18 UTC  
> Url: https://github.com/boostorg/charconv/issues/191#issuecomment-2071976915  

Yes of course. I'm not saying this needs to be changed.  
  
But you should know that `check_cxx_source_compiles` and `check_cxx_source_runs` are just wrappers over `try_compile` and `try_run`.  
  
https://github.com/Kitware/CMake/blob/master/Modules/CheckCXXSourceCompiles.cmake  
https://github.com/Kitware/CMake/blob/master/Modules/Internal/CheckSourceCompiles.cmake  
  
https://github.com/Kitware/CMake/blob/master/Modules/CheckCXXSourceRuns.cmake  
https://github.com/Kitware/CMake/blob/master/Modules/Internal/CheckSourceRuns.cmake
