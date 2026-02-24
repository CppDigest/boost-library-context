# #293 msvc build fix. [Closed]

> Username: lysevi  
> Created at: 2017-03-31 05:55:58 UTC  
> Updated at: 2017-04-19 16:59:14 UTC  
> Closed at: 2017-04-01 14:13:09 UTC  
> Url: https://github.com/boostorg/beast/pull/293  

build on msvc 2015.

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2017-03-31 17:30:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/293#pullrequestreview-30302740  

📁 CMakeLists.txt

```diff
  35 |+ set(Boost_USE_MULTITHREADED ON)
  36 |+ 
  37 |+ find_package(Boost REQUIRED COMPONENTS regex coroutine context thread filesystem program_options system)
```

> Username: vinniefalco  
> Created_at: 2017-03-31 17:30:57 UTC  
> Url: https://github.com/boostorg/beast/pull/293#discussion_r109210963  

why regex?

> Username: lysevi  
> Created_at: 2017-03-31 18:07:04 UTC  
> Url: https://github.com/boostorg/beast/pull/293#discussion_r109218411  

regex need to work boost::asio::read_until

> Username: vinniefalco  
> Created_at: 2017-04-01 13:55:44 UTC  
> Url: https://github.com/boostorg/beast/pull/293#discussion_r109285497  

None of the Beast code calls `boost::asio::read_until` - where do you see it?


---

## Comment 2

> Username: vinniefalco  
> Created_at: 2017-03-31 17:42:30 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290779755  

I like the idea, but I get these warnings when I run CMake on Windows now:  
```  
$ cmake ..  
CMake Warning at C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 106300  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:37 (find_package)  
```  
  
Any ideas?

---

## Comment 3

> Username: lysevi  
> Created_at: 2017-03-31 18:08:47 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290786431  

may be need change to  
  
```cmake  
find_package(Boost COMPONENTS regex coroutine context thread filesystem program_options system)  
```

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-03-31 18:29:06 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290791708  

Same problem:  
```  
$ cmake ..  
CMake Warning at C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:743 (message):  
  Imported targets not available for Boost version 106300  
Call Stack (most recent call first):  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:842 (_Boost_COMPONENT_DEPENDENCIES)  
  C:/Program Files/CMake/share/cmake-3.6/Modules/FindBoost.cmake:1395 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:37 (find_package)  
```

---

## Comment 5

> Username: harrishancock  
> Created_at: 2017-03-31 21:42:56 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290837536  

@vinniefalco try upgrading your CMake. According to this SO question, you need CMake 3.7 to find Boost 1.63.  
  
http://stackoverflow.com/questions/42123509/cmake-finds-boost-but-the-imported-targets-not-available-for-boost-version

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-03-31 22:02:28 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290842446  

How will CMake find my Boost in Windows exactly? Does Boost have to be in a well-known location?

---

## Comment 7

> Username: harrishancock  
> Created_at: 2017-03-31 22:26:09 UTC  
> Updated_at: 2017-03-31 22:27:53 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290846534  

I don't think there's any well-known location. The usual way I do it is:  
  
1. unzip Boost to `/path/to/boost`  
2. cd to `/path/to/boost` and run `bootstrap.bat` and `b2 stage` (with appropriate parameters)  
3. `export BOOST_ROOT=/path/to/boost`  
  
You could also `b2 install` Boost somewhere instead of staging it in its build directory, in which case that install dir would become `BOOST_ROOT`. I find that to be useful when building multiple types of Boost (e.g., 32-, 64-bit) from the same source. Lastly, I'm sure there are precompiled Boost distributions available for MSVC, too, though I haven't used them.  
  
I think most other ways to do it involve using a package manager. I've had good luck with Hunter, although it builds the entirety of Boost during the CMake configure process. Conan and Vcpkg both have ways to do it, but I haven't tried them yet.  
  
Update: these are the gory details of how CMake finds it: https://cmake.org/cmake/help/latest/module/FindBoost.html

---

## Comment 8

> Username: lysevi  
> Created_at: 2017-04-01 05:41:11 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290897319  

Cmake search boost in -DBOOST_ROOT="path/to/boost/"

---

## Comment 9

> Username: ras0219-msft  
> Created_at: 2017-04-01 10:21:51 UTC  
> Updated_at: 2017-04-01 10:22:05 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290911284  

I'd recommend using  
```cmake  
option(Boost_USE_STATIC_LIBS "Use static libraries for boost" ON)  
```  
This enables users to disable it from the command line if they wish. This is important for vcpkg users, because we install DLLs for Boost by default.  
  
CMake documentation on `option()`: https://cmake.org/cmake/help/v3.0/command/option.html  
  
@vinniefalco CMake searches a _ton_ of locations for Boost. The most common way for Windows users is probably via the `%BOOST_ROOT%` environment variable, with `-DBOOST_ROOT` being a close second. For anyone using vcpkg (`-DCMAKE_TOOLCHAIN_FILE=..../vcpkg.cmake`) we set `CMAKE_PREFIX_PATH` which also gets searched.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2017-04-01 13:31:13 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290920265  

Okay, I upgraded to CMake 3.7.2, and I like these changes! I've incorporated the option as per @ras0219-msft suggestion (thanks for that). Question, should I use `REQUIRED` here or not:  
```  
find_package(Boost COMPONENTS regex coroutine context thread filesystem program_options system)  
```  
??

---

## Comment 11

> Username: ras0219-msft  
> Created_at: 2017-04-01 13:33:10 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290920357  

Yep, `REQUIRED` is the best way to alert the user that the build cannot continue when they don't have Boost.

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2017-04-01 13:33:44 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290920390  

Should I put `REQUIRED` on **all** calls to `find_package` or just the Boost one?

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2017-04-01 13:35:41 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290920499  

err... OpenSSL is optional so I guess I'm asking about  
```  
find_package(REQUIRED Threads)  
```  
  
I think I need it because otherwise, gcc/clang builds get link errors

---

## Comment 14

> Username: ras0219-msft  
> Created_at: 2017-04-01 13:39:08 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290920672  

Yeah, it probably should be (though honestly I don't think I've ever seen that particular `find_package()` fail...).

---

## Comment 15

> Username: vinniefalco  
> Created_at: 2017-04-01 14:13:09 UTC  
> Updated_at: 2017-04-01 14:13:45 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-290922595  

Part of https://github.com/vinniefalco/Beast/pull/294  
  
If you'd like to help out with a code review on **1.0.0-b32** (here: https://github.com/vinniefalco/Beast/pull/294) that would be great, especially the cmake changes!  
  
Thanks everyone!!!

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2017-04-19 16:59:14 UTC  
> Url: https://github.com/boostorg/beast/pull/293#issuecomment-295345027  

These changes have messed up my Visual Studio RelWithDebInfo builds, investigating...

---
