# #86 Mingw build [Closed]

> Username: e-fominov  
> Created at: 2016-09-23 11:55:20 UTC  
> Updated at: 2016-09-25 15:03:23 UTC  
> Closed at: 2016-09-25 15:03:23 UTC  
> Url: https://github.com/boostorg/beast/pull/86  

I was unable to build Beast as-is with MinGW and MSCV. This PR makes it possible  
Tested with MINGW 6.2 x64, and MSVC 2015 x64, Windows 7

---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-09-23 12:07:50 UTC  
> Updated_at: 2016-09-25 14:59:23 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249175026  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/86?src=pr) is 98.11% (diff: 100%)  
  
> Merging [#86](https://codecov.io/gh/vinniefalco/Beast/pull/86?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will increase coverage by **0.04%**  
  
``` diff  
@@             master        #86   diff @@  
==========================================  
  Files            71         71            
  Lines          4036       4033     -3     
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
- Hits           3958       3957     -1     
+ Misses           78         76     -2     
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [2f9a844...0711d3b](https://codecov.io/gh/vinniefalco/Beast/compare/2f9a8440c2432d8a196571d6300404cb76314125...0711d3b2d80c23e148c73d4d8ce834fabf2316b3?src=pr)

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-09-23 12:07:58 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249175046  

[![Coverage Status](https://coveralls.io/builds/8021506/badge)](https://coveralls.io/builds/8021506)  
  
Coverage increased (+0.05%) to 98.116% when pulling **c5b0c7fe647b5220e200fd9833bf9de783826586 on e-fominov:mingw_build** into **2f9a8440c2432d8a196571d6300404cb76314125 on vinniefalco:master**.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-09-25 02:27:05 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249398689  

Thanks! This looks very promising, although realistically I have no way to test it since I don't use MinGW. Is there some way to have MinGW as a target on Travis?

---

## Comment 4

> Username: e-fominov  
> Created_at: 2016-09-25 10:40:41 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249414613  

I dont have much experience with Travis. MinGW itself is GCC-based and there will be no syntax differences, except the libc is missing on MinGW.  
  
MinGW has cross-compiler that can be installed into Linux platform and also I found one stackoverflow post where it seems possible to use MinGW with Travis: http://stackoverflow.com/questions/28155308/how-to-cross-compile-with-mingw-on-linux-for-windows

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-09-25 12:30:42 UTC  
> Updated_at: 2016-09-25 12:32:33 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249419280  

Hmm...unfortunately these changes break 64-bit MSVC builds and I'm not sure why. I get link errors with library mismatches. This is the command line I used to build the solution and project files after cherry-picking your commits:  
  
```  
cd bin64  
cmake -G"Visual Studio 14 2015 Win64" ..  
```  
  
Typical errors:  
  
```  
5>D:\lib\boost_1_61_0\stage\lib\libboost_coroutine-vc140-mt-gd-1_61.lib : warning LNK4272: library machine type 'X86' conflicts with target machine type 'x64'  
5>D:\lib\boost_1_61_0\stage\lib\libboost_context-vc140-mt-gd-1_61.lib : warning LNK4272: library machine type 'X86' conflicts with target machine type 'x64'  
5>D:\lib\boost_1_61_0\stage\lib\libboost_thread-vc140-mt-gd-1_61.lib : warning LNK4272: library machine type 'X86' conflicts with target machine type 'x64'  
5>D:\lib\boost_1_61_0\stage\lib\libboost_filesystem-vc140-mt-gd-1_61.lib : warning LNK4272: library machine type 'X86' conflicts with target machine type 'x64'  
5  
```

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-09-25 12:40:32 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249419706  

I think I see what's happening. These are the lines causing problems:  
  
```  
find_package(Boost REQUIRED COMPONENTS coroutine context thread filesystem program_options system)  
link_directories(${Boost_LIBRARY_DIRS})  
link_libraries(${Boost_LIBRARIES})  
```  
  
Unfortunately with MSVC, we can't use the package finder to set the library directories, because there is no way to distinguish between 32 versus 64 bit builds. What I do is add a path to the Boost 32 bit libraries in the Microsoft.Cpp.Win32.user property sheet, and add a different path to the Boost 64 bit libraries in the Microsoft.Cpp.x64.user property sheet. If we were to use find_package and link_directories for this, then targeting both 32 and 64 bit builds in separate solutions would no longer be possible.  
  
These are the steps I use to generate the separate solutions, please try them and see if they work with your changes:  
  
```  
cd bin  
cmake ..  
cd ../bin64  
cmake -G"Visual Studio 14 2015 Win64" ..  
```

---

## Comment 7

> Username: e-fominov  
> Created_at: 2016-09-25 12:44:03 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249419867  

Do you have boost compiled for x64?   I have tested this PR with MSVCx64 - compiles well. But my boost is compiled for x64  
Looks like D:\lib\boost_1_61_0\stage\lib\ libraries on your machine are x86  
  
This problem is because your original CMakeLists.txt file had no find_module(Boost) for Win32 compilation. Looks like you have something in your System Environment variables that makes it find boost in incorrect place. Normally CMake's findboost module searches it in c:\boost  
  
Now it tries to find boost and finds it in D:\lib\boost_1_61_0\stage\lib\, which contain x86 libraries  
To make it use Boost from other folder, its possible to pass additional params to cmake:  
  
```  
cd bin64  
cmake -G"Visual Studio 14 2015 Win64" .. -DBOOST_INCLUDEDIR=d:\boost64\include -DBOOST_LIBRARYDIR=d:\boost64\lib  
```

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2016-09-25 12:47:20 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249420003  

I have 32-bit boost libraries in `D:\lib\boost_1_61_0\stage` and 64-bit boost libraries in `d:\lib\boost_1_61_0\stage64`. I also have `$BOOST_ROOT` set to `/d/lib/boost_1_61_0/`.  
  
You're telling me that I should manually specify the header include and library include paths every time I want to rebuild the visual studio project files?

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2016-09-25 13:04:25 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249420794  

I tried this line with your changes:  
  
```  
cmake -G"Visual Studio 14 2015 Win64" .. -DBOOST_INCLUDEDIR=D:\lib\bo  
ost_1_61_0 -DBOOST_LIBRARYDIR=d:\lib\boost_1_61_0\stage64  
```  
  
But the Additional Library Directories under Linker properties shows  
  
```  
D:/lib/boost_1_61_0/stage/lib;D:/lib/boost_1_61_0/stage/lib/$(Configuration);%(AdditionalLibraryDirectories)  
```

---

## Comment 10

> Username: e-fominov  
> Created_at: 2016-09-25 13:18:07 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249421435  

Try adding -DBoost_NO_SYSTEM_PATHS=ON  
  
What is the version of CMake you are using? If your version < 3.6.2, it may not know about boost version 1.61. You can tell him that this version exists:  
  
-DBoost_ADDITIONAL_VERSIONS=1.61

---

## Comment 11

> Username: e-fominov  
> Created_at: 2016-09-25 13:22:53 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249421676  

I have just checked out master branch CMakeFiles.txt, and MSVC told me that he cant compile:  
  
```  
C:\work\Beast\include\beast/core/detail/ci_char_traits.hpp(11): fatal error C1083: Unable to open include file: boost/range/algorithm/equal.hpp: No such file or directory (compiling C:\work\Beast\test\http\parser_bench.cpp) [C:\work\Beast\b15\test\http\bench-tests.vcxproj]  
...  
```  
  
Are you sure, it was compiling with previous version of CMakeFiles.txt?

---

## Comment 12

> Username: vinniefalco  
> Created_at: 2016-09-25 13:36:01 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249422211  

I'm on CMake 3.6.0. I will try updating it.  
  
Yes, I am sure that the tip of _master_ compiles with CMake. I do all of my development in Visual Studio and I use CMake to generate the project files. You need to add the include path and library path to your Microsoft.Cpp.x64.user property sheet (View -> Property Manager).

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2016-09-25 13:40:17 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249422408  

Updating to CMake 3.6.2 had no effect on the result, I get the same errors. This is the command line I used:  
  
```  
cd bin64  
rm -rf *  
cmake -G"Visual Studio 14 2015 Win64" .. -DBOOST_INCLUDEDIR=D:\lib\boost_1_61_0 -DBOOST_LIBRARYDIR=d:\lib\boost_1_61_0\stage64  
```  
  
This is what the linker properties on the resulting project look like:  
![image](https://cloud.githubusercontent.com/assets/1503976/18815579/05777684-8304-11e6-886f-1f67705395ef.png)

---

## Comment 14

> Username: e-fominov  
> Created_at: 2016-09-25 14:43:21 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249425574  

I have restored original code for MSVC compilation process. MinGW compiles well on my pc  
To compile MSVC I need to open .vcproj in Studio and set directories manually

---

## Comment 15

> Username: coveralls  
> Created_at: 2016-09-25 14:59:19 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249426472  

[![Coverage Status](https://coveralls.io/builds/8040317/badge)](https://coveralls.io/builds/8040317)  
  
Coverage increased (+0.05%) to 98.116% when pulling **0711d3b2d80c23e148c73d4d8ce834fabf2316b3 on e-fominov:mingw_build** into **2f9a8440c2432d8a196571d6300404cb76314125 on vinniefalco:master**.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2016-09-25 15:03:23 UTC  
> Url: https://github.com/boostorg/beast/pull/86#issuecomment-249426658  

This works for me now:  
https://github.com/vinniefalco/Beast/commits/mingw  
  
It will go into b14, so I'll close this. Thanks for your contribution!

---
