# #87 - windbg: has no member named 'back' [Closed]

> Username: andry81  
> Created at: 2020-02-20 00:45:57 UTC  
> Updated at: 2020-02-24 14:57:53 UTC  
> Closed at: 2020-02-24 14:57:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/87  

Boost: `1.72.0`  
OS: `Windows 7 x64`  
Compiler: `mingw/gcc 5.3.0`  
  
```  
PATH: "C:/Windows/system32"  
PATH: "C:/Windows"  
PATH: "C:/Windows/system32/Wbem"  
PATH: "g:/Qt/Qt5_11_1/Tools/mingw530_32/bin"  
PATH: "c:/Program Files (x86)/Microsoft SDKs/Windows/v7.1A/bin"  
>pushd "d:/_3dparty/mingw_gcc_x86/utility/boost/boost_1_72_0-build"  
>"d:/_3dparty/_src/utility/boost/boost_1_72_0/b2.exe" "-sBOOST_ROOT=d:/_3dparty/_src/utility/boost/boost_1_72_0"  variant=release,debug toolset=gcc-mingw architecture=x86 address-model=32 link=shared runtime-link=shared threading=multi debug-symbols=on "--build-dir=__build-mingw_gcc-32" stage  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : x86  
    - C++11 mutex              : no  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  
    - Boost.Config Feature Check: cxx11_constexpr : no  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  
    - Boost.Config Feature Check: cxx11_final : no  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  
    - Boost.Config Feature Check: cxx11_lambdas : no  
    - Boost.Config Feature Check: cxx11_noexcept : no  
    - Boost.Config Feature Check: cxx11_nullptr : no  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  
    - Boost.Config Feature Check: cxx11_template_aliases : no  
    - Boost.Config Feature Check: cxx11_thread_local : no  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  
    - has_icu builds           : no  
  
...  
```  
  
```  
gcc.compile.c++ __build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden\windbg.o  
In file included from libs\stacktrace\build\..\src\windbg.cpp:9:0:  
./boost/stacktrace/detail/frame_msvc.ipp: In function 'void boost::stacktrace::detail::trim_right_zeroes(std::__cxx11::string&)':  
./boost/stacktrace/detail/frame_msvc.ipp:118:14: error: 'std::__cxx11::string {aka class std::__cxx11::basic_string<char>}' has no member named 'back'  
     while (s.back() == '\0') {  
              ^  
./boost/stacktrace/detail/frame_msvc.ipp:119:11: error: 'std::__cxx11::string {aka class std::__cxx11::basic_string<char>}' has no member named 'pop_back'  
         s.pop_back();  
           ^  
  
    "g++-mingw"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_DYN_LINK=1  -I"." -c -o "__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden\windbg.o" "libs\stacktrace\build\..\src\windbg.cpp"  
  
...failed gcc.compile.c++ __build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden\windbg.o...  
...skipped <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll.a for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\mingw_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll.a for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll.a...  
...skipped <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-mgw-mt-d-x32-1_72-shared.cmake for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll.a...  
...skipped <pD:\_3dparty\mingw_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib\cmake\boost_stacktrace_windbg-1.72.0>libboost_stacktrace_windbg-variant-mgw-mt-d-x32-1_72-shared.cmake for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-variant-mgw-mt-d-x32-1_72-shared.cmake...  
...skipped <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>windbg.o...  
...skipped <pD:\_3dparty\mingw_gcc_x86\utility\boost\boost_1_72_0-build\stage\lib>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll for lack of <p__build-mingw_gcc-32\boost\bin.v2\libs\stacktrace\build\gcc-mingw\debug\threading-multi\visibility-hidden>libboost_stacktrace_windbg-mgw-mt-d-x32-1_72.dll...  
```  
  
All other libraries builds without issues.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-02-24 14:57:53 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/87#issuecomment-590362429  

This was fixed in develop and master branches https://github.com/boostorg/stacktrace/commit/7379a5cc08e578a5c33cf8a0e3f8d00075f6a1d6  
  
Fix will appear in next Boost release
