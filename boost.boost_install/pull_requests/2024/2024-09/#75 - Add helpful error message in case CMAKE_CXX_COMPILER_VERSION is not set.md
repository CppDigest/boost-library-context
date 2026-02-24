# #75 Add helpful error message in case CMAKE_CXX_COMPILER_VERSION is not set [Open]

> Username: LeSpocky  
> Created at: 2024-09-09 10:20:27 UTC  
> Updated at: 2024-09-09 10:41:37 UTC  
> Url: https://github.com/boostorg/boost_install/pull/75  

CMake bails out with an error message giving no clue what's going on, if variable CMAKE_CXX_COMPILER_VERSION is not set:  
  
    CMake Error at /usr/lib/x86_64-linux-gnu/cmake/BoostDetectToolset-1.74.0.cmake:9 (string):  
      string sub-command REGEX, mode MATCHALL needs at least 5 arguments total to  
      command.  
    Call Stack (most recent call first):  
      /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.74.0/boost_thread-config.cmake:27 (include)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:141 (find_package)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:258 (boost_find_component)  
      CMakeLists.txt:7 (find_package)  
  
    -- Boost toolset is unknown (compiler  )  
  
The added message still leads to a CMake error, but gives the user a better clue where to look for in her own project trying to find Boost.  
  
Output afterwards:  
  
    CMake Error at /usr/lib/x86_64-linux-gnu/cmake/BoostDetectToolset-1.74.0.cmake:6 (message):  
      CMAKE_CXX_COMPILER_VERSION is not set!  
    Call Stack (most recent call first):  
      /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.74.0/boost_thread-config.cmake:27 (include)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:141 (find_package)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:258 (boost_find_component)  
      CMakeLists.txt:7 (find_package)  
  
    CMake Error at /usr/lib/x86_64-linux-gnu/cmake/BoostDetectToolset-1.74.0.cmake:8 (string):  
      string sub-command REGEX, mode MATCHALL needs at least 5 arguments total to  
      command.  
    Call Stack (most recent call first):  
      /usr/lib/x86_64-linux-gnu/cmake/boost_thread-1.74.0/boost_thread-config.cmake:27 (include)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:141 (find_package)  
      /usr/lib/x86_64-linux-gnu/cmake/Boost-1.74.0/BoostConfig.cmake:258 (boost_find_component)  
      CMakeLists.txt:7 (find_package)  
  
    -- Boost toolset is unknown (compiler  )  
  
Fixes: boostorg/boost_install#76

---

## Comment 1

> Username: LeSpocky  
> Created_at: 2024-09-09 10:21:37 UTC  
> Url: https://github.com/boostorg/boost_install/pull/75#issuecomment-2337727200  

Not sure whether `message(FATAL_ERROR "blabla")` would make more sense here, or a whole different approach to compiler version detection?  Let me know.

---

## Comment 2

> Username: LeSpocky  
> Created_at: 2024-09-09 10:41:36 UTC  
> Url: https://github.com/boostorg/boost_install/pull/75#issuecomment-2337766408  

`if(DEFINED …` would have been better?

---
