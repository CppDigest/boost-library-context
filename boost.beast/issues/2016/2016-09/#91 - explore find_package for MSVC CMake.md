# #91 - explore find_package for MSVC CMake [Closed]

> Username: vinniefalco  
> Created at: 2016-09-25 15:02:42 UTC  
> Updated at: 2017-04-11 01:52:00 UTC  
> Closed at: 2017-04-11 01:52:00 UTC  
> Url: https://github.com/boostorg/beast/issues/91  

It would be nice to use find_package for Boost when building Visual Studio projects but I couldn't get that to work and support both 32 and 64 bit compiled boost and projects on the same machine.

---

## Comment 1

> Username: e-fominov  
> Created at: 2016-09-26 09:47:31 UTC  
> Url: https://github.com/boostorg/beast/issues/91#issuecomment-249526266  

I have tested several ways of solution and found one possible way: do not use "stage" folder for building boos libs  
1. Build boost:  
  
```  
b2 address-model=64 --build-type=minimal stage --stagedir=stage64  
b2 address-model=32 --build-type=minimal stage --stagedir=stage32  
```  
  
Its important to keep x86 libraries in "stage32" not "stage" folder  
2. Put additional flags to Cmake when building Beast:  
  
```  
cmake .. -G"Visual Studio 14 2015" -DBOOST_ROOT=d:\lib\boost_1_61_0 -DBOOST_LIBRARYDIR=d:\libs\boost_1_61_0\stage32\lib  
cmake .. -G"Visual Studio 14 2015 Win64" -DBOOST_ROOT=d:\lib\boost_1_61_0 -DBOOST_LIBRARYDIR=d:\libs\boost_1_61_0\stage64\lib  
```  
  
Now it correctly finds boost and creates correct vsproj files  
  
If machine has only one boost and its installed in default folder - no need in additional flags when running cmake. This solution only when you have two boosts  
  
To check if it finds boost correctly you can try temptorary modifying cmakelists.txt file:  
  
```  
    set(Boost_USE_STATIC_LIBS ON)  
    set(Boost_USE_MULTITHREADED ON)  
    find_package(Boost REQUIRED COMPONENTS coroutine context thread filesystem program_options system)  
    message(${Boost_LIBRARY_DIRS})  
```  
  
add this before "if(MSVC)" and it will print found library path  
  
After boost will be found correctly we need modify CMakeLists.txt to add boost libraries to linker (now it does not have them)

---

## Comment 2

> Username: e-fominov  
> Created at: 2016-09-26 10:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/91#issuecomment-249535641  

and one more way to solve this:  
  
```  
if(CMAKE_SIZEOF_VOID_P EQUAL 8)  
    set(Boost_LIBRARY_DIR ${BOOST_ROOT}/stage64/lib)  
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)  
    set(Boost_LIBRARY_DIR ${BOOST_ROOT}/stage/lib)  
endif()  
```  
  
this code should be called befor find_package(boost) and will initialize library dir automatically based on MSVC version, but require to have BOOST_ROOT set, that can be got from Environment

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-04-11 01:52:00 UTC  
> Url: https://github.com/boostorg/beast/issues/91#issuecomment-293126874  

Beast **1.0.0-b32** now supports finding Boost packages on Windows!
