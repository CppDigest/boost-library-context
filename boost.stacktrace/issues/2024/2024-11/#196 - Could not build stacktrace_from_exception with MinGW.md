# #196 - Could not build stacktrace_from_exception with MinGW [Closed]

> Username: uilianries  
> Created at: 2024-11-21 15:08:50 UTC  
> Updated at: 2025-02-18 13:06:09 UTC  
> Closed at: 2025-02-18 10:41:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/196  

### Summary  
  
Greetings!   
  
I am reporting a bug encountered when attempting to build stacktrace with MinGW while the `boost.stacktrace.from_exception` feature is enabled. This issue does not occur with MSVC on Windows. The error was identified while trying to incorporate this feature into the Conan recipe for Boost in [ConanCenterIndex](https://github.com/conan-io/conan-center-index/blob/master/recipes/boost/all/conanfile.py)  
  
### Error Details:  
  
During the build, the follow error occurs:  
  
```  
gcc.compile.c++ C:\Users\uilia\.conan2\p\b\boosta27812415810e\b\build-release\boost\bin.v2\libs\stacktrace\build\gcc-13\rls\x86_6\cxstd-14-gnu\lnk-sttc\nm-on\thrdp-wn32\thrd-mlt\vsblt-hdn\from_exception.o  
  
    "C:/Users/uilia/.conan2/p/mingwf3629a0c4f698/p/bin/g++.exe"   -std=gnu++14 -fvisibility-inlines-hidden -m64 -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fPIC -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_USE_NUMA -DNDEBUG -D_GLIBCXX_USE_CXX11_ABI=1   -I"."  -c -o "C:\Users\uilia\.conan2\p\b\boosta27812415810e\b\build-release\boost\bin.v2\libs\stacktrace\build\gcc-13\rls\x86_6\cxstd-14-gnu\lnk-sttc\nm-on\thrdp-wn32\thrd-mlt\vsblt-hdn\from_exception.o" "libs/stacktrace/build/../src/from_exception.cpp"  
  
libs/stacktrace/build/../src/from_exception.cpp:160:10: fatal error: dlfcn.h: No such file or directory  
  160 | #include <dlfcn.h>  
      |          ^~~~~~~~~  
compilation terminated.  
```  
  
I see in the file `location_from_symbol.hpp` there is a conditional to avoid the very same error:  
  
https://github.com/boostorg/stacktrace/blob/boost-1.87.0.beta1/include/boost/stacktrace/detail/location_from_symbol.hpp#L15  
  
The same is not present for `from_exception.cpp`, only an initial ifdef for `_MSC_VER`, a define that's for MSVC.   
  
### Environment  
  
OS: Windows 10 Pro 22H2  
Arch: amd64 (x86_64)  
Build type: Release  
Compiler: GCC 13  
  
The MinGW version used by me can be found on Github: https://github.com/niXman/mingw-builds-binaries/releases/download/13.2.0-rt_v11-rev1/x86_64-13.2.0-release-mcf-seh-ucrt-rt_v11-rev1.7z  
  
  
### Attachments:  
  
For further details, please refer to my full build log: [boost-1.86.0-mingw.log](https://github.com/user-attachments/files/17847731/boost-1.86.0-mingw.log)  
  
Thank you for your attention to this matter. I look forward to any guidance or solutions you might provide.

---

## Comment 1

> Username: crhilton  
> Created at: 2025-02-17 16:28:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/196#issuecomment-2663590931  

I'm also getting this, cross compiling from Linux with GCC 13 and MingW 11.  It's our own build of gcc and mingw, so I can't share it.  
  
I'm not sure what the right fix will be, but appending to the #if defined(_MSV_VER) worked for me:  
#if defined(__MINGW32__) || defined(_MSC_VER)  
  
At least well enough to get both a mingw cross compile and a linux native compile.

---

## Comment 2

> Username: apolukhin  
> Created at: 2025-02-18 10:42:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/196#issuecomment-2665257654  

> I'm not sure what the right fix will be, but appending to the #if defined(_MSV_VER) worked for me: #if defined(**MINGW32**) || defined(_MSC_VER)  
>   
> At least well enough to get both a mingw cross compile and a linux native compile.  
  
Many thanks for the fix!

---

## Comment 3

> Username: uilianries  
> Created at: 2025-02-18 13:06:07 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/196#issuecomment-2665664385  

@apolukhin Thank you for fixing it!
