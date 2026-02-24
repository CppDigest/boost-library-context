# #28 - 'windbg_cached.o' compilation error for mingw-w64 builds on Windows [Closed]

> Username: sav-ix  
> Created at: 2017-08-22 11:47:35 UTC  
> Updated at: 2017-10-13 18:36:57 UTC  
> Closed at: 2017-10-13 18:36:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/28  

Hello everyone,  
  
For Boost.Stacktrace builds using mingw-w64 got error:  
```  
gcc.compile.c++ bin.v2\libs\stacktrace\build\gcc-mingw-6.3.0\release\threading-multi\windbg_cached.o  
  
    "g++"   -O3 -finline-functions -Wno-inline -Wall -pedantic -mthreads -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2\libs\stacktrace\build\gcc-mingw-6.3.0\release\threading-multi\windbg_cached.o" "libs\stacktrace\build\..\src\windbg_cached.cpp"  
  
In file included from libs\stacktrace\build\..\src\windbg_cached.cpp:10:0:  
./boost/stacktrace/detail/frame_msvc.ipp:167:72: warning: missing terminating ' character  
 #   error Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
                                                                        ^  
./boost/stacktrace/detail/frame_msvc.ipp:167:5: error: #error Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
 #   error Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
     ^~~~~  
In file included from ./boost/detail/winapi/file_management.hpp:17:0,  
                 from ./boost/stacktrace/detail/safe_dump_win.ipp:20,  
                 from ./boost/stacktrace/safe_dump_to.hpp:206,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:15,  
                 from libs\stacktrace\build\..\src\windbg_cached.cpp:10:  
./boost/detail/winapi/overlapped.hpp:39:9: warning: ISO C++ prohibits anonymous structs [-Wpedantic]  
         };  
         ^  
...failed gcc.compile.c++ bin.v2\libs\stacktrace\build\gcc-mingw-6.3.0\release\threading-multi\windbg_cached.o...  
...failed updating 1 target...  
...updated 14074 targets...  
```  
  
Reproduced for:  
* [Boost-1.65.0](https://dl.bintray.com/boostorg/release/1.65.0/source/boost_1_65_0.tar.gz) builds using mingw-w64 with `<Debug|Release><Shared|Static>` configurations.  
  
Not reproduced for:  
* builds using ICC or MSVC,  
* [Boost-1.65.0-beta1_rc2](https://dl.bintray.com/boostorg/beta/1.65.0.beta.1/source/boost_1_65_0_beta1_rc2.tar.gz) builds using mingw-w64.  
  
Environment:  
* Windows 10 x64,  
* mingw-w64 6.3.0 x86_64,  
* [Boost-1.65.0](https://dl.bintray.com/boostorg/release/1.65.0/source/boost_1_65_0.tar.gz).  
  
  
Alexander

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-08-22 20:00:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/28#issuecomment-324135334  

It was fixed in this two commits : https://github.com/boostorg/stacktrace/commit/cd420b37ae7e84196cdb8202e489d5d276c55f0b and https://github.com/boostorg/stacktrace/commit/b84d6533f5c5f0a2b2737c6511ecd602a957cd95  
  
However they did not make it into the 1.65 release. As a quick workaround you can build Boost without the Stacktrace library (mingw-w64 is not supported at this moment, so it would not probably work. Add `--without-stacktrace` to your `./b2` build command to disable Stacktrace builds).

---

## Comment 2

> Username: sav-ix  
> Created at: 2017-08-23 05:02:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/28#issuecomment-324221651  

Applied mentioned PRs to Boost-1.65.0 sources, and **Boost.Stacktrace** builds using mingw-w64 finished successfully.  
Thank you for fix, Antony!  
  
BTW, **Boost.Fiber** was built using mingw-w64 in Boost-1.65.0-beta1_rc2 and not build in Boost-1.65.0. Similarly to **Boost.Stacktrace**. Just wondering, is it related to this issue or should be reported in [appropriate](https://github.com/boostorg/fiber) topic?

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-08-26 21:16:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/28#issuecomment-325162354  

> Just wondering, is it related to this issue or should be reported in appropriate topic  
  
Looks like a separate issue. Please report it in a separate topic.  
  
Great thanks for reporting the issues!

---

## Comment 4

> Username: sav-ix  
> Created at: 2017-08-27 08:06:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/28#issuecomment-325183607  

And thank you for maintaining part of Boost Project.  
  
Since https://github.com/boostorg/stacktrace/commit/cd420b37ae7e84196cdb8202e489d5d276c55f0b and https://github.com/boostorg/stacktrace/commit/b84d6533f5c5f0a2b2737c6511ecd602a957cd95 are already in master, I assume this issue could be closed.
