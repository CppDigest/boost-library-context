# #716 - CMake Error at CMakeLists.txt:20 (include):   include could not find requested file:      BoostRoot [Closed]

> Username: brendoncdrew  
> Created at: 2022-12-01 22:57:25 UTC  
> Updated at: 2025-10-14 05:51:39 UTC  
> Closed at: 2022-12-01 23:41:21 UTC  
> Url: https://github.com/boostorg/boost/issues/716  

bdrew@HpEE:~/source/boost$ cmake ./  
-- The CXX compiler identification is GNU 12.2.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:20 (include):  
  include could not find requested file:  
  
    BoostRoot  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/bdrew/source/boost/CMakeFiles/CMakeOutput.log".

---

## Comment 1

> Username: brendoncdrew  
> Created at: 2022-12-01 23:42:23 UTC  
> Url: https://github.com/boostorg/boost/issues/716#issuecomment-1334585021  

RTFM

---

## Comment 2

> Username: excitoon  
> Created at: 2023-02-02 14:53:37 UTC  
> Url: https://github.com/boostorg/boost/issues/716#issuecomment-1413870613  

Is there any solution to this?

---

## Comment 3

> Username: excitoon  
> Created at: 2023-02-02 14:57:07 UTC  
> Url: https://github.com/boostorg/boost/issues/716#issuecomment-1413876003  

@brendoncdrew you probably keep it in some hidden branch, can you point us out?

---

## Comment 4

> Username: Kk-Knight  
> Created at: 2025-09-28 06:38:02 UTC  
> Url: https://github.com/boostorg/boost/issues/716#issuecomment-3342383418  

You can refer to the official Boost CMake support repository:  
https://github.com/boostorg/cmake  
  
When cloning the Boost source code, it’s recommended to use the recursive submodules option, because Boost contains many libraries managed as git submodules.  
If you don’t fetch them recursively, you may run into missing dependencies or build failures.  
  
Example command:  
`git clone --recurse-submodules https://github.com/boostorg/boost  
`  
  
This will make sure all submodules are downloaded automatically, so you have a complete source tree for building Boost.

---

## Comment 5

> Username: akategdev  
> Created at: 2025-10-14 05:51:39 UTC  
> Url: https://github.com/boostorg/boost/issues/716#issuecomment-3400220070  

> You can refer to the official Boost CMake support repository: https://github.com/boostorg/cmake  
>   
> When cloning the Boost source code, it’s recommended to use the recursive submodules option, because Boost contains many libraries managed as git submodules. If you don’t fetch them recursively, you may run into missing dependencies or build failures.  
>   
> Example command: `git clone --recurse-submodules https://github.com/boostorg/boost `  
>   
> This will make sure all submodules are downloaded automatically, so you have a complete source tree for building Boost.  
  
This actually worked.
