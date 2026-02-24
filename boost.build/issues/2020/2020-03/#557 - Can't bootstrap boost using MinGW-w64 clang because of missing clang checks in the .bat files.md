# #557 - Can't bootstrap boost using MinGW-w64 clang because of missing clang checks in the .bat files [Open]

> Username: tomay3000  
> Created at: 2020-03-27 22:27:43 UTC  
> Updated at: 2021-05-29 18:22:22 UTC  
> Url: https://github.com/boostorg/build/issues/557  

When you try to bootstrap `bootstrap.bat clang` boost using `MinGW-w64 clang` [https://github.com/mstorsjo/llvm-mingw](https://github.com/mstorsjo/llvm-mingw) (not GCC), the bootstrap will fail complaining about an unknown toolset.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-03-28 01:13:13 UTC  
> Url: https://github.com/boostorg/build/issues/557#issuecomment-605373225  

Thanks for reporting this. I can't promise that I can deal with it quickly as dealing with covid-19 is making things difficult.

---

## Comment 2

> Username: tomay3000  
> Created at: 2020-03-28 13:22:11 UTC  
> Updated at: 2020-03-28 13:23:03 UTC  
> Url: https://github.com/boostorg/build/issues/557#issuecomment-605446834  

> Thanks for reporting this. I can't promise that I can deal with it quickly as dealing with covid-19 is making things difficult.  
  
Yeah, covid-19 sucks.  
  
Do you have any ideas on how I can work around it?  
  
TIA.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2020-03-28 16:24:15 UTC  
> Url: https://github.com/boostorg/build/issues/557#issuecomment-605475769  

> Do you have any ideas on how I can work around it?  
You should be able to force it to use clang for the B2 build itself. But you'll have to go the long route and build B2 independently of Boost first. To do that you can:  
```  
cd tools\build  
set CXX=\path\to\clang.exe  
.\bootstrap.bat mingw  
cd ..\..  
```  
And use the `tools\build\b2.exe` as the executable for building Boost. Not sure about what else you might have to do for bootstrapping the setup of Building the Boost libs themselves. But at minimum you'll have to create a user-config.jam or project-config.jam to set up the mingw clang you are using.  
  
An entirely different alternative is to install the free edition of Visual Studio. And bootstrap with that. But then build the Boost libs with you mingw clang (you'll need the *-config.jam file for that).

---

## Comment 4

> Username: tomay3000  
> Created at: 2020-03-28 22:32:21 UTC  
> Url: https://github.com/boostorg/build/issues/557#issuecomment-605528345  

Thanks for the info.  
I have tried this and it built all the `.a` libraries except the `.dll` ones:  
```  
1 - Open cmd and add MinGW-w64 toolchain to your PATH environment variable ("set PATH=%PATH%;C:/llvm-mingw/bin/").  
2 - Go to the directory "./tools/build/".  
3 - Run "set CXX=clang++".  
4 - Run "bootstrap.bat gcc".  
6 - Run "b2 install --prefix=PREFIX" or simply run: "b2 install" where PREFIX is the directory where you want Boost.Build to be installed. (By default PREFIX="C:/boost-build/bin/").  
7 - Change your current directory to the Boost root directory or simply run: "cd ../.." and invoke b2 as follows:  
    ".\tools\build\b2 --build-dir=build-directory toolset=toolset-name --build-type=complete stage", or simply run:  
    ".\tools\build\b2 toolset=gcc --build-type=complete stage".  
  
The option "--build-type=complete" causes Boost.Build to build all supported variants of the libraries. For instructions on how to build only specific variants, please ask on the Boost.Build mailing list.  
Building the special stage target places Boost library binaries in the "./stage/lib/" subdirectory of the Boost tree. To use a different directory pass the "--stagedir=directory" option to b2.  
```  
  
And here are the last lines of the build log:  
```  
gcc.compile.c++ bin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\scheduler.o  
...skipped <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\context\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contex  
t-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_fiber-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>li  
bboost_fiber-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib\cmake\boost_fiber-1.72.0>libboost_fiber-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-variant-mgw100-mt-  
d-x32-1_72-shared.cmake...  
...skipped <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\context\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_context-  
mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_fiber-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\fiber\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_fiber-mgw100-mt-d-x32-1_72.dll...  
gcc.compile.c++ bin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\graphml.o  
gcc.compile.c++ bin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\read_graphviz_new.o  
...skipped <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\regex\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_regex-mg  
w100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_graph-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>li  
bboost_graph-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib\cmake\boost_graph-1.72.0>libboost_graph-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-variant-mgw100-mt-  
d-x32-1_72-shared.cmake...  
...skipped <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\regex\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_regex-mgw1  
00-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_graph-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\graph\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_graph-mgw100-mt-d-x32-1_72.dll...  
...failed updating 248 targets...  
...skipped 816 targets...  
...updated 5964 targets...  
  
C:\Dev\boost\boost_1_72_0>  
```  
  
Launching `.\tools\build\b2 toolset=gcc --build-type=complete stage` again without cleaning, then the following error messages appears:  
```  
C:\llvm-mingw\lib\clang\10.0.0\include\emmintrin.h:16:19: note: '__m128i' declared here  
typedef long long __m128i __attribute__((__vector_size__(16), __aligned__(16)));  
                  ^  
4 errors generated.  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTRACT_DYN_LINK  -I"." -c -o "bin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\c  
ontract.o" "libs\contract\src\contract.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\contract.o...  
...skipped <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>contract  
.o...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_contract-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-  
hidden>libboost_contract-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib\cmake\boost_contract-1.72.0>libboost_contract-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-varian  
t-mgw100-mt-d-x32-1_72-shared.cmake...  
...skipped <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>contract.o  
...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_contract-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\contract\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_contract-mgw100-mt-d-x32-1_72.dll...  
gcc.compile.c++ bin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\mapped_file.o  
In file included from libs\iostreams\src\mapped_file.cpp:19:  
In file included from .\boost/iostreams/device/mapped_file.hpp:20:  
In file included from .\boost/iostreams/close.hpp:19:  
In file included from .\boost/iostreams/detail/adapter/non_blocking_adapter.hpp:13:  
In file included from .\boost/iostreams/seek.hpp:23:  
.\boost/iostreams/positioning.hpp:52:10: error: no matching constructor for initialization of 'std::streampos' (aka 'fpos<_Mbstatet>')  
{ return std::streampos(std::mbstate_t(), off); }  
         ^              ~~~~~~~~~~~~~~~~~~~~~  
C:\llvm-mingw\include\c++\v1\string:543:31: note: candidate constructor not viable: allows at most single argument '__off', but 2 arguments were provided  
    _LIBCPP_INLINE_VISIBILITY fpos(streamoff __off = streamoff()) : __st_(), __off_(__off) {}  
                              ^  
C:\llvm-mingw\include\c++\v1\string:537:28: note: candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 2 were provided  
class _LIBCPP_TEMPLATE_VIS fpos  
                           ^  
C:\llvm-mingw\include\c++\v1\string:537:28: note: candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 2 were provided  
In file included from libs\iostreams\src\mapped_file.cpp:19:  
In file included from .\boost/iostreams/device/mapped_file.hpp:20:  
In file included from .\boost/iostreams/close.hpp:19:  
In file included from .\boost/iostreams/detail/adapter/non_blocking_adapter.hpp:13:  
In file included from .\boost/iostreams/seek.hpp:23:  
.\boost/iostreams/positioning.hpp:96:16: error: no member named 'seekpos' in 'std::__1::fpos<_Mbstatet>'  
    return pos.seekpos();  
           ~~~ ^  
2 errors generated.  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_IOSTREAMS_DYN_LINK=1 -DBOOST_IOSTREAMS_LZMA_NO_MULTITHREADED=1 -DBOOST_IOSTREAMS_USE_DEPRECATED  -I"." -c -o "bin.v2\libs\iostre  
ams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\mapped_file.o" "libs\iostreams\src\mapped_file.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden\mapped_file.o...  
...skipped <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>file_  
descriptor.o...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_iostreams-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibili  
ty-hidden>libboost_iostreams-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib\cmake\boost_iostreams-1.72.0>libboost_iostreams-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-va  
riant-mgw100-mt-d-x32-1_72-shared.cmake...  
...skipped <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>file_de  
scriptor.o...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_iostreams-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\iostreams\build\gcc-10.0.0\debug\address-model-32\threading-multi\visibility-hidden>libboost_iostreams-mgw100-mt-d-x32-1_72.dll...  
gcc.compile.c++ bin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden\windows\stack_traits.o  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
In file included from .\boost/thread/win32/thread_primitives.hpp:18:  
In file included from .\boost/detail/interlocked.hpp:79:  
In file included from C:\llvm-mingw\lib\clang\10.0.0\include\intrin.h:12:  
C:\llvm-mingw\include\intrin.h:87:15: error: definition of type '__m64' conflicts with typedef of the same name  
typedef union __m64 { char v[7]; } __m64;  
              ^  
C:\llvm-mingw\lib\clang\10.0.0\include\mmintrin.h:13:19: note: '__m64' declared here  
typedef long long __m64 __attribute__((__vector_size__(8), __aligned__(8)));  
                  ^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
In file included from .\boost/thread/win32/thread_primitives.hpp:18:  
In file included from .\boost/detail/interlocked.hpp:79:  
In file included from C:\llvm-mingw\lib\clang\10.0.0\include\intrin.h:12:  
C:\llvm-mingw\include\intrin.h:90:15: error: definition of type '__m128' conflicts with typedef of the same name  
typedef union __m128 { char v[16]; } __m128;  
              ^  
C:\llvm-mingw\lib\clang\10.0.0\include\xmmintrin.h:17:15: note: '__m128' declared here  
typedef float __m128 __attribute__((__vector_size__(16), __aligned__(16)));  
              ^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
In file included from .\boost/thread/win32/thread_primitives.hpp:18:  
In file included from .\boost/detail/interlocked.hpp:79:  
In file included from C:\llvm-mingw\lib\clang\10.0.0\include\intrin.h:12:  
C:\llvm-mingw\include\intrin.h:93:15: error: definition of type '__m128d' conflicts with typedef of the same name  
typedef union __m128d { char v[16]; } __m128d;  
              ^  
C:\llvm-mingw\lib\clang\10.0.0\include\emmintrin.h:15:16: note: '__m128d' declared here  
typedef double __m128d __attribute__((__vector_size__(16), __aligned__(16)));  
               ^  
In file included from libs\coroutine\src\windows\stack_traits.cpp:23:  
In file included from .\boost/thread.hpp:13:  
In file included from .\boost/thread/thread.hpp:12:  
In file included from .\boost/thread/thread_only.hpp:15:  
In file included from .\boost/thread/win32/thread_data.hpp:11:  
In file included from .\boost/thread/win32/thread_primitives.hpp:18:  
In file included from .\boost/detail/interlocked.hpp:79:  
In file included from C:\llvm-mingw\lib\clang\10.0.0\include\intrin.h:12:  
C:\llvm-mingw\include\intrin.h:94:15: error: definition of type '__m128i' conflicts with typedef of the same name  
typedef union __m128i { char v[16]; } __m128i;  
              ^  
C:\llvm-mingw\lib\clang\10.0.0\include\emmintrin.h:16:19: note: '__m128i' declared here  
typedef long long __m128i __attribute__((__vector_size__(16), __aligned__(16)));  
                  ^  
4 errors generated.  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_COROUTINES_DYN_LINK=1 -DBOOST_COROUTINES_SOURCE -DBOOST_THREAD_BUILD_DLL=1  
-DBOOST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32  -I"." -c -o "bin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden\windows\stack_traits.o" "libs\coroutine\src\windows\stack_traits.cpp"  
  
...failed gcc.compile.c++ bin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden\windows\stack_traits.o...  
...skipped <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libboost_coroutine-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threadi  
ng-multi\visibility-hidden>windows\stack_traits.o...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_coroutine-mgw100-mt-d-x32-1_72.dll.a for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libboost_coroutine-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libboost_coroutine-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadap  
i-win32\threading-multi\visibility-hidden>libboost_coroutine-mgw100-mt-d-x32-1_72.dll.a...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib\cmake\boost_coroutine-1.72.0>libboost_coroutine-variant-mgw100-mt-d-x32-1_72-shared.cmake for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libbo  
ost_coroutine-variant-mgw100-mt-d-x32-1_72-shared.cmake...  
...skipped <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libboost_coroutine-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading  
-multi\visibility-hidden>windows\stack_traits.o...  
...skipped <pC:\Dev\boost\boost_1_72_0\stage\lib>libboost_coroutine-mgw100-mt-d-x32-1_72.dll for lack of <pbin.v2\libs\coroutine\build\gcc-10.0.0\debug\address-model-32\threadapi-win32\threading-multi\visibility-hidden>libboost_coroutine-mgw100-mt-d-x32-1_72.dll...  
...failed updating 248 targets...  
...skipped 816 targets...  
  
C:\Dev\boost\boost_1_72_0>  
```

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:52 UTC  
> Url: https://github.com/boostorg/build/issues/557#issuecomment-850877277  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
