# #88 - cygwin: `#error "Boost.Stacktrace requires `_Unwind_Backtrace` function.` [Closed]

> Username: andry81  
> Created at: 2020-02-20 16:52:08 UTC  
> Updated at: 2024-09-12 08:05:34 UTC  
> Closed at: 2024-09-12 08:04:59 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88  

Boost: `1.72.0`  
OS: `Windows 7 x64`, `cygwin dll 3.1.2`  
Compiler: `cygwin/gcc 7.4.0`  
  
```  
PATH: "C:/Windows/system32"  
PATH: "C:/Windows"  
PATH: "C:/Windows/system32/Wbem"  
PATH: "c:/cygwin/3.0.x/bin"  
PATH: "c:/Program Files (x86)/Microsoft SDKs/Windows/v7.1A/bin"  
>pushd "d:/_3dparty/cygwin_gcc_x86/utility/boost/boost_1_72_0-build"  
>"d:/_3dparty/_src/utility/boost/boost_1_72_0/b2.exe" "-sBOOST_ROOT=d:/_3dparty/_src/utility/boost/boost_1_72_0"  variant=release,debug toolset=gcc architecture=x86 address-model=32 link=shared runtime-link=shared threading=multi debug-symbols=on "--build-dir=__build-cygwin_gcc-32" stage  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : x86  
    - default address-model    : 32-bit  
    - default architecture     : x86  
    - C++11 mutex              : yes  
    - lockfree boost::atomic_flag : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_constexpr : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_final : yes  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_noexcept : yes  
    - Boost.Config Feature Check: cxx11_nullptr : yes  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes  
    - Boost.Config Feature Check: cxx11_template_aliases : yes  
    - Boost.Config Feature Check: cxx11_thread_local : yes  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes  
    - has_icu builds           : no  
...  
```  
```  
gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o  
In file included from ./boost/stacktrace/safe_dump_to.hpp:217:0,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:15,  
                 from libs\stacktrace\build\..\src\windbg_cached.cpp:10:  
./boost/stacktrace/detail/collect_unwind.ipp:33:2: error: #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
  ^~~~~  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1 -DNDEBUG  -I"." -c -o "__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o" "libs\stacktrace\build\..\src\windbg_cached.cpp"  
  
...failed gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>windbg_cached.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll.a...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-variant-gcc7-mt-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll.a...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_stacktrace_windbg_cached-1.72.0>libboost_stacktrace_windbg_cached-variant-gcc7-mt-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-variant-gcc7-mt-x32-1_72-shared.cmake...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>windbg_cached.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>cygboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg_cached-gcc7-mt-x32-1_72.dll...  
common.copy D:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_locale-1.72.0\libboost_locale-variant-gcc7-mt-x32-1_72-shared.cmake  
__build-cygwin_gcc-32\boost\bin.v2\libs\locale\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\libboost_locale-variant-gcc7-mt-x32-1_72-shared.cmake  
Скопировано файлов:         1.  
gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg.o  
In file included from ./boost/stacktrace/safe_dump_to.hpp:217:0,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:15,  
                 from libs\stacktrace\build\..\src\windbg.cpp:9:  
./boost/stacktrace/detail/collect_unwind.ipp:33:2: error: #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
  ^~~~~  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1 -DNDEBUG  -I"." -c -o "__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg.o" "libs\stacktrace\build\..\src\windbg.cpp"  
  
...failed gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden\windbg.o...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll.a...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-gcc7-mt-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll.a...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_stacktrace_windbg-1.72.0>libboost_stacktrace_windbg-variant-gcc7-mt-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-gcc7-mt-x32-1_72-shared.cmake...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>cygboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\release\debug-symbols-on\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg-gcc7-mt-x32-1_72.dll...  
```  
```  
gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg.o  
In file included from ./boost/stacktrace/safe_dump_to.hpp:217:0,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:15,  
                 from libs\stacktrace\build\..\src\windbg.cpp:9:  
./boost/stacktrace/detail/collect_unwind.ipp:33:2: error: #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
  ^~~~~  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1  -I"." -c -o "__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg.o" "libs\stacktrace\build\..\src\windbg.cpp"  
  
...failed gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg.o...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll.a...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-gcc7-mt-d-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll.a...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_stacktrace_windbg-1.72.0>libboost_stacktrace_windbg-variant-gcc7-mt-d-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-gcc7-mt-d-x32-1_72-shared.cmake...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>cygboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg-gcc7-mt-d-x32-1_72.dll...  
gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\serialization\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\xml_grammar.o  
gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o  
In file included from ./boost/stacktrace/safe_dump_to.hpp:217:0,  
                 from ./boost/stacktrace/frame.hpp:20,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:15,  
                 from libs\stacktrace\build\..\src\windbg_cached.cpp:10:  
./boost/stacktrace/detail/collect_unwind.ipp:33:2: error: #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 #error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
  ^~~~~  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1  -I"." -c -o "__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o" "libs\stacktrace\build\..\src\windbg_cached.cpp"  
  
...failed gcc.compile.c++ __build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden\windbg_cached.o...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>windbg_cached.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll.a for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll.a...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-variant-gcc7-mt-d-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll.a...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_stacktrace_windbg_cached-1.72.0>libboost_stacktrace_windbg_cached-variant-gcc7-mt-d-x32-1_72-shared.cmake for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>libboost_stacktrace_windbg_cached-variant-gcc7-mt-d-x32-1_72-shared.cmake...  
...skipped <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>windbg_cached.o...  
...skipped <pD:\_3dparty\cygwin_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>cygboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll for lack of <p__build-cygwin_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-7.4.0\debug\target-os-cygwin\threading-multi\visibility-hidden>cygboost_stacktrace_windbg_cached-gcc7-mt-d-x32-1_72.dll...  
```  
  
All other libraries builds without errors.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-02-20 17:21:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88#issuecomment-589209624  

Have you tried defining `_GNU_SOURCE` ?

---

## Comment 2

> Username: andry81  
> Created at: 2020-02-20 17:56:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88#issuecomment-589225479  

> Have you tried defining `_GNU_SOURCE` ?  
  
I don't build specifically this library, I just wanted to build the boost library as a whole. And I think it's should be built without errors by default without any macroses for a specific library, so I reported the issue.

---

## Comment 3

> Username: RamakrishnanOSS  
> Created at: 2020-04-17 12:23:58 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88#issuecomment-615215012  

> Have you tried defining `_GNU_SOURCE` ?  
  
I had the same problem. I did tried with -D_GNU_SOURCE=1 it did worked. But what could be the root cause of this _GNU_SOURCE is not enabled ?  
  
May be Cygwin support can be added to stacktrace lib itself.  
Thanks.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-06-01 01:05:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88#issuecomment-851737426  

`backtrace.cpp` defines `_GNU_SOURCE`:  
  
https://github.com/boostorg/stacktrace/blob/1be59df18e82d0f0e988c89568e5763e964d3f31/src/backtrace.cpp#L11-L13  
  
but `windbg.cpp` and `windbg_cached.cpp` do not. It's possible to define this from the outside, e.g. in the build system, but it would probably be more consistent to define it in the source files, at least when `__CYGWIN__` is defined, to match what `backtrace.cpp` does.

---

## Comment 5

> Username: apolukhin  
> Created at: 2024-09-12 08:05:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/88#issuecomment-2345548545  

Defined _GNU_SOURCE unconditionally. Looks like the same issue was on MacOS platform.
