# #6 - No possibility to use CMake FetchContent [Closed]

> Username: adamvm  
> Created at: 2020-02-17 16:58:49 UTC  
> Updated at: 2023-12-31 00:03:25 UTC  
> Closed at: 2020-05-11 15:14:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/6  

Currently it is not possible to use FetchContent module/functions of CMake.

---

## Comment 1

> Username: NeroBurner  
> Created at: 2020-04-29 13:34:50 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-621210731  

Could you elaborate why and give a minimal viable CMake project that reproduces your error?

---

## Comment 2

> Username: adamvm  
> Created at: 2020-04-29 17:07:07 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-621342674  

So Cmake from boost report this itself, it's not a bug, but lack of feature that is reported

---

## Comment 3

> Username: NeroBurner  
> Created at: 2020-04-29 17:28:06 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-621353837  

do you mean you can't add boost to a separate project (which uses cmake as build configuration tool) and have boost dependency handled by FetchContent? Wouldn't this mean Boost must be built completely by CMake to have it available by using `add_subdirectory()`?

---

## Comment 4

> Username: adamvm  
> Created at: 2020-05-01 09:08:04 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-622311693  

I will reproduce this and report here

---

## Comment 5

> Username: adamvm  
> Created at: 2020-05-10 06:21:04 UTC  
> Updated at: 2020-05-10 06:21:41 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-626280314  

Here is output:  
```  
/snap/clion/112/bin/cmake/linux/bin/cmake -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - Unix Makefiles" /home/adi/dev/coders/boostFetch  
CMake Error at cmake-build-debug/_deps/boostorg-src/tools/cmake/include/BoostRoot.cmake:13 (message):  
  CMake support in Boost is experimental and part of an ongoing development  
  effort.  It's not ready for use yet.  Please use b2 (Boost.Build) to build  
  and install Boost.  
Call Stack (most recent call first):  
  cmake-build-debug/_deps/boostorg-src/CMakeLists.txt:14 (include)  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/adi/dev/coders/boostFetch/cmake-build-debug/CMakeFiles/CMakeOutput.log".  
```  
  
Here is CMakeLists.txt part:  
```  
cmake_minimum_required(VERSION 3.16)  
project(boostFetch)  
  
include(FetchContent)  
  
set(CMAKE_CXX_STANDARD 17)  
  
FetchContent_Declare(  
        boostorg  
        GIT_REPOSITORY https://github.com/boostorg/boost.git  
        GIT_TAG        master  
)  
  
FetchContent_GetProperties(boostorg)  
if (NOT boostorg_POPULATED)  
    FetchContent_Populate(boostorg)  
    add_subdirectory(${boostorg_SOURCE_DIR} ${boostorg_BINARY_DIR})  
endif ()  
  
include_directories(${boostorg_SOURCE_DIR})  
```

---

## Comment 6

> Username: pdimov  
> Created at: 2020-05-10 16:28:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-626353520  

Since Boost's support for CMake is still experimental, it's intentionally disabled by default. To enable it, add `set(BOOST_ENABLE_CMAKE ON)` to your CMakeLists.txt file, somewhere before the `add_subdirectory` call.

---

## Comment 7

> Username: excitoon  
> Created at: 2023-02-02 15:06:38 UTC  
> Updated at: 2023-02-02 15:07:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1413892921  

@pdimov @brendoncdrew I tried this:  
```  
set (BOOST_ENABLE_CMAKE ON)  
FetchContent_Declare (boost URL "https://github.com/boostorg/boost/archive/da041154c6bac1a4aa98254a7d6819059e8ac0b0.zip")  
FetchContent_MakeAvailable (boost)  
```  
  
It gives:  
```  
CMake Error at /home/vladimir/some-project-bin/_deps/boost-src/CMakeLists.txt:20 (include):  
  include could not find load file:  
  
    BoostRoot  
  
  
-- Configuring incomplete, errors occurred!  
See also "/home/vladimir/some-project-bin/CMakeFiles/CMakeOutput.log".  
```  
  
Any suggestions? Probably it is somehow connected with boostorg/boost#716.

---

## Comment 8

> Username: excitoon  
> Created at: 2023-02-02 15:18:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1413911008  

I see, I should have selected release sources, not sources sources :)  
![Screenshot from 2023-02-02 18-16-18](https://user-images.githubusercontent.com/2255111/216364710-4adcefad-2894-4280-be91-1ddd5d2dc1fa.png)

---

## Comment 9

> Username: excitoon  
> Created at: 2023-02-02 15:26:08 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1413922844  

It successfully compiles without any problem on random i686 MinGW cross-compilation environment. I greatly appreciate your work @pdimov .

---

## Comment 10

> Username: felixf4xu  
> Created at: 2023-07-15 07:33:06 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1636698699  

>  should have selected release sources, not sources sources :)  
  
please also note that this should be from **release** not **tag**, file size is very different!!  
  
>[boost-1.82.0.7z](https://github.com/boostorg/boost/releases/download/boost-1.82.0/boost-1.82.0.7z)  
88.5 MB  
Apr 14  
[boost-1.82.0.tar.gz](https://github.com/boostorg/boost/releases/download/boost-1.82.0/boost-1.82.0.tar.gz)  
116 MB  
Apr 14  
[boost-1.82.0.tar.xz](https://github.com/boostorg/boost/releases/download/boost-1.82.0/boost-1.82.0.tar.xz)  
88.2 MB  
Apr 14  
[boost-1.82.0.zip](https://github.com/boostorg/boost/releases/download/boost-1.82.0/boost-1.82.0.zip)  
147 MB  
Apr 14  
Source code  
(zip)  
Apr 6  
Source code  
(tar.gz)  
Apr 6

---

## Comment 11

> Username: melroy89  
> Created at: 2023-12-22 01:06:27 UTC  
> Updated at: 2023-12-22 01:10:18 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867105151  

Here is an example how to use it, it doesn't seem to be documented well enough IMO:  
  
```cmake  
set(BOOST_INCLUDE_LIBRARIES asio regex algorithm)  
set(BOOST_ENABLE_CMAKE ON)  
FetchContent_Declare(  
  Boost  
  URL https://github.com/boostorg/boost/releases/download/boost-1.84.0/boost-1.84.0.tar.xz  
  USES_TERMINAL_DOWNLOAD TRUE  
  DOWNLOAD_NO_EXTRACT FALSE  
)  
FetchContent_MakeAvailable(Boost)  
```  
  
Then link the library you want to the target (example: `Boost::asio` link to `your_target` executable):  
  
```cmake  
target_link_libraries(your_target Boost::asio)  
```  
  
Maybe this helps somebody.

---

## Comment 12

> Username: pdimov  
> Created at: 2023-12-22 01:09:56 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867107296  

It's documented [here](https://github.com/boostorg/cmake#using-boost-with-fetchcontent).

---

## Comment 13

> Username: melroy89  
> Created at: 2023-12-22 01:13:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867109488  

> It's documented [here](https://github.com/boostorg/cmake#using-boost-with-fetchcontent).  
  
Yes true sorry, it was not explained on the official boostorg/boost repo is what I meant. OK, I understand you want to keep that clean.  
  
What about `DOWNLOAD_NO_EXTRACT FALSE`? Could that speed up some what? I switched from 7z to tar.xz now. Maybe is there more speed to gain somehow?

---

## Comment 14

> Username: pdimov  
> Created at: 2023-12-22 01:19:18 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867112607  

7z and tar.xz should be about the same. I can't think of any way to speed that up, sorry. It's still a 90MB download in any case.

---

## Comment 15

> Username: melroy89  
> Created at: 2023-12-22 01:21:58 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867114057  

Instead of a mono-repo maybe it's a good idea to split the repo in smaller chunks, I only wish to use asio and regex basically. Just thinking out loud here, since the repo size and the artifacts in general are getting bigger and bigger over time. At least not smaller ;)

---

## Comment 16

> Username: pdimov  
> Created at: 2023-12-22 01:42:13 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867125683  

You can clone from Github instead of downloading the release archive. This is usually much slower, but if you only need a few submodules, it could be faster. Something like  
  
```  
FetchContent_Declare(  
  Boost  
  GIT_REPOSITORY https://github.com/boostorg/boost.git  
  GIT_TAG boost-1.84.0  
  GIT_SUBMODULES asio regex  
)  
```  
but that's not going to work because you'll also need the dependencies of Asio, which at present are...  
```  
C:\boost-git\develop>dist\bin\boostdep --brief asio  
Brief dependency report for asio (sources on, tests off):  
  
# Primary dependencies  
  
align  
assert  
config  
context  
coroutine  
date_time  
system  
throw_exception  
  
# Secondary dependencies  
  
core  
static_assert  
mp11  
pool  
predef  
smart_ptr  
exception  
move  
type_traits  
utility  
algorithm  
io  
lexical_cast  
numeric~conversion  
range  
tokenizer  
winapi  
variant2  
array  
bind  
concept_check  
function  
iterator  
mpl  
regex  
tuple  
unordered  
container  
integer  
conversion  
preprocessor  
container_hash  
detail  
optional  
intrusive  
describe  
function_types  
fusion  
functional  
typeof  
```  
  
Sorry. :-)

---

## Comment 17

> Username: melroy89  
> Created at: 2023-12-22 02:54:46 UTC  
> Updated at: 2023-12-31 00:01:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1867168121  

No worries. Dependency graph:  
  
![depedencies](https://github.com/boostorg/cmake/assets/628926/37c536b7-d559-40c0-97c6-0e669b8c95a2)

---

## Comment 18

> Username: melroy89  
> Created at: 2023-12-31 00:01:08 UTC  
> Updated at: 2023-12-31 00:03:25 UTC  
> Url: https://github.com/boostorg/cmake/issues/6#issuecomment-1872629164  

> You can clone from Github instead of downloading the release archive. This is usually much slower, but if you only need a few submodules, it could be faster. Something like  
>   
> ```  
> FetchContent_Declare(  
>   Boost  
>   GIT_REPOSITORY https://github.com/boostorg/boost.git  
>   GIT_TAG boost-1.84.0  
>   GIT_SUBMODULES asio regex  
> )  
> ```  
>   
> but that's not going to work because you'll also need the dependencies of Asio, which at present are...  
>   
> ```  
> C:\boost-git\develop>dist\bin\boostdep --brief asio  
> Brief dependency report for asio (sources on, tests off):  
>   
> # Primary dependencies  
>   
> align  
> assert  
> config  
> context  
> coroutine  
> date_time  
> system  
> throw_exception  
>   
> # Secondary dependencies  
>   
> core  
> static_assert  
> mp11  
> pool  
> predef  
> smart_ptr  
> exception  
> move  
> type_traits  
> utility  
> algorithm  
> io  
> lexical_cast  
> numeric~conversion  
> range  
> tokenizer  
> winapi  
> variant2  
> array  
> bind  
> concept_check  
> function  
> iterator  
> mpl  
> regex  
> tuple  
> unordered  
> container  
> integer  
> conversion  
> preprocessor  
> container_hash  
> detail  
> optional  
> intrusive  
> describe  
> function_types  
> fusion  
> functional  
> typeof  
> ```  
>   
> Sorry. :-)  
  
I actually just found out that ASIO is a separate library, which doesn't need Boost: https://github.com/chriskohlhoff/asio/  
  
More info: http://think-async.com/Asio/AsioStandalone.html
