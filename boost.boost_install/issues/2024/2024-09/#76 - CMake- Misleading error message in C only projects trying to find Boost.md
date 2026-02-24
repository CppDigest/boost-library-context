# #76 - CMake: Misleading error message in C only projects trying to find Boost [Open]

> Username: LeSpocky  
> Created at: 2024-09-09 09:22:28 UTC  
> Updated at: 2024-09-09 14:54:26 UTC  
> Url: https://github.com/boostorg/boost_install/issues/76  

I have multiple CMake library projects here, one of them using Boost threads.  One library project ("A") exports its own config together with a `find_dependency(Boost COMPONENTS thread)` call.  
  
Another library project ("B") uses `find_package(A)` and this way the find_dependency is executed which calls `find_package(Boost COMPONENTS thread)` internally.  That is then used by a third library ("C"), so you a have a chain of dependencies here.  
  
Problem: library C currently has its LANGUAGES set to `C` in the `project()` call.  Linking libB in libC is possible, but since the switch from CMake FindBoost module to cmake package config coming with Boost I get a build error, which I could track down to Boost.  
  
Minimal example requires a short *CMakeLists.txt* only:  
  
```cmake  
cmake_minimum_required(VERSION 3.5)  
project(bt  
    VERSION 0.1  
    LANGUAGES C  
)  
  
find_package(Boost REQUIRED CONFIG  
    COMPONENTS  
        thread  
)  
```  
  
The error message is like this:  
  
```  
CMake Error at /usr/lib/x86_64-linux-gnu/cmake/BoostDetectToolset-1.74.0.cmake:5 (string):  
  string sub-command REGEX, mode MATCHALL needs at least 5 arguments total to  
  command.  
Call Stack (most recent call first):  
  /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.74.0/boost_thread-config.cmake:27 (include)  
  /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:141 (find_package)  
  /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:258 (boost_find_component)  
  CMakeLists.txt:7 (find_package)  
  
  
Boost toolset is unknown (compiler  )  
CMake Error at /usr/lib/x86_64-linux-gnu/cmake/BoostDetectToolset-1.74.0.cmake:5 (string):  
  string sub-command REGEX, mode MATCHALL needs at least 5 arguments total to  
  command.  
Call Stack (most recent call first):  
  /usr/lib/x86_64-linux-gnu/cmake/boost_atomic-1.74.0/boost_atomic-config.cmake:27 (include)  
  /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.74.0/boost_thread-config.cmake:100 (find_package)  
  /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:141 (find_package)  
  /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:258 (boost_find_component)  
  CMakeLists.txt:7 (find_package)  
  
  
Boost toolset is unknown (compiler  )  
Performing Test CMAKE_HAVE_LIBC_PTHREAD  
Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
Found Threads: TRUE    
Configuring incomplete, errors occurred!  
See also "/home/adahl/build/tmp/bt/CMakeFiles/CMakeOutput.log".  
```  
  
*Note:* this still happens with most recent Boost, it was just easier to reproduce on my host machine like this.  
  
*Note:* this only happens if COMPONENTS are involved.  
  
Analysis: the problem is in *tools/boost_install/BoostDetectToolset.cmake* right in the first line:  
  
```cmake  
string(REGEX MATCHALL "[0-9]+" _BOOST_COMPILER_VERSION ${CMAKE_CXX_COMPILER_VERSION})  
```  
  
In my case `${CMAKE_CXX_COMPILER_VERSION}` is empty here (C only project), leading to that error message.  I would propose to check for that variable before using it and bailout with a meaningful error message if it is found to be empty.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-09 10:21:29 UTC  
> Url: https://github.com/boostorg/boost_install/issues/76#issuecomment-2338348968  

Something like this: https://github.com/boostorg/boost_install/commit/d85e1b557adb9c4d70f0c6fabb670a5a2d9bc12c?

---

## Comment 2

> Username: LeSpocky  
> Created at: 2024-09-09 10:24:46 UTC  
> Url: https://github.com/boostorg/boost_install/issues/76#issuecomment-2338348976  

> Something like this: [d85e1b5](https://github.com/boostorg/boost_install/commit/d85e1b557adb9c4d70f0c6fabb670a5a2d9bc12c)?  
  
Wow you are really quick.  I could go with that.  Opened my own PR meanwhile (#75), lunch break was between issue and pull request.  Sorry for the redundancy.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-09-09 14:53:59 UTC  
> Url: https://github.com/boostorg/boost_install/issues/76#issuecomment-2338348987  

Please don't move my issues around, @mclow ; it was in the right place.
