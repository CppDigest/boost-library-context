# #1711 - thread library is not linked on ubuntu 16.04 [Closed]

> Username: fcaponetto  
> Created at: 2019-09-25 12:26:53 UTC  
> Updated at: 2019-11-01 15:33:03 UTC  
> Closed at: 2019-11-01 15:33:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1711  

Hi guys!  
I tried to compile  the source code based on the commit  **55fd67b**  
Unfortunately the linker was not able to link the "thread library" properly to the examples executable.  
In particular, just for test, i built _http-server-small_  
  
On the root CmakeLists.txt you have:  
> set (THREADS_PREFER_PTHREAD_FLAG ON)  
   find_package (Threads)  
   set( CMAKE_CXX_FLAGS  
   "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter")  
    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")  
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wrange-loop-analysis")  
    endif ()  
  
For fixing this problem i had to introduce the -**pthread** option on _CMAKE_CXX_FLAGS_ flags:  
> set( CMAKE_CXX_FLAGS  
     "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter -**pthread**")

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-09-25 12:33:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1711#issuecomment-534999831  

The CMakeLists.txt only supports Windows. Try the Jamfile?

---

## Comment 2

> Username: fcaponetto  
> Created at: 2019-09-25 13:15:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1711#issuecomment-535016607  

In the root CMakeLists.txt there is the if/else condition:  
  
>if (MSVC)  
...  
else()  
    set (THREADS_PREFER_PTHREAD_FLAG ON)  
    find_package (Threads)  
    set( CMAKE_CXX_FLAGS  
      "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wpedantic -Wno-unused-parameter")  
    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")  
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wrange-loop-analysis")  
    endif ()  
endif()  
  
... so i thought that cmake is also compatible for Linux.  
  
However, adding that flags mentioned in my first message (-pthread) in Linux system cmake works.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-09-25 14:51:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1711#issuecomment-535060664  

I guess I can add it, but does this break other environments? I simply copied and pasted the `else` part. Or perhaps someone else contributed it, I can't recall. Since this file is not tested except on Windows, there's no way to evaluate the change.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-10-25 15:11:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1711#issuecomment-546393948  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-11-01 15:33:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1711#issuecomment-548833015  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
