# #102 - msys2: `'basic_debug_output_backend' does not name a type` [Closed]

> Username: andry81  
> Created at: 2020-02-20 16:59:31 UTC  
> Updated at: 2020-02-24 08:14:29 UTC  
> Closed at: 2020-02-22 08:09:35 UTC  
> Url: https://github.com/boostorg/log/issues/102  

Boost: `1.72.0`  
OS: `Windows 7 x64`  
Compiler: `msys32/gcc 7.4.0`  
Msys installer: `https://www.msys2.org`, `msys2-i686-20190524`  
  
```  
PATH: "C:/Windows/system32"  
PATH: "C:/Windows"  
PATH: "C:/Windows/system32/Wbem"  
PATH: "c:/msys32/20190524/usr/bin"  
>pushd "D:/_3dparty/msys_gcc_x86/utility/boost/boost_1_72_0-build"  
>"D:/_3dparty/_src/utility/boost/boost_1_72_0/b2.exe" "-sBOOST_ROOT=D:/_3dparty/_src/utility/boost/boost_1_72_0"  variant=release,debug toolset=gcc architecture=x86 address-model=32 link=shared runtime-link=shared threading=multi debug-symbols=on "--build-dir=__build-msys_gcc-32" stage  
Performing configuration checks  
  
    - default address-model    : 32-bit  
    - default architecture     : x86  
    - C++11 mutex              : yes  
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
gcc.compile.c++ __build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\release\debug-symbols-on\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o  
libs\log\src\windows\debug_output_backend.cpp:47:15: error: 'basic_debug_output_backend' does not name a type  
 BOOST_LOG_API basic_debug_output_backend< CharT >::basic_debug_output_backend()  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:52:15: error: 'basic_debug_output_backend' does not name a type  
 BOOST_LOG_API basic_debug_output_backend< CharT >::~basic_debug_output_backend()  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:58:46: error: expected initializer before '<' token  
 BOOST_LOG_API void basic_debug_output_backend< CharT >::consume(record_view const&, string_type const& formatted_message)  
                                              ^  
libs\log\src\windows\debug_output_backend.cpp:64:16: error: 'basic_debug_output_backend' is not a class template  
 template class basic_debug_output_backend< char >;  
                ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:64:16: error: explicit instantiation of non-template type 'boost::log::v2_mt_nt6::sinks::basic_debug_output_backend'  
libs\log\src\windows\debug_output_backend.cpp:67:16: error: 'basic_debug_output_backend' is not a class template  
 template class basic_debug_output_backend< wchar_t >;  
                ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:67:16: error: explicit instantiation of non-template type 'boost::log::v2_mt_nt6::sinks::basic_debug_output_backend'  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -march=i686 -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32 -DBOOST_USE_WINDOWS_H -DDATE_TIME_INLINE -DNDEBUG -DNOMINMAX -DSECURITY_WIN32 -DWIN32_LEAN_AND_MEAN -D__STDC_CONSTANT_MACROS  -I"." -I"libs\log\src" -c -o "__build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\release\debug-symbols-on\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o" "libs\log\src\windows\debug_output_backend.cpp"  
  
...failed gcc.compile.c++ __build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\release\debug-symbols-on\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o...  
```  
```  
gcc.compile.c++ __build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\debug\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o  
libs\log\src\windows\debug_output_backend.cpp:47:15: error: 'basic_debug_output_backend' does not name a type  
 BOOST_LOG_API basic_debug_output_backend< CharT >::basic_debug_output_backend()  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:52:15: error: 'basic_debug_output_backend' does not name a type  
 BOOST_LOG_API basic_debug_output_backend< CharT >::~basic_debug_output_backend()  
               ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:58:46: error: expected initializer before '<' token  
 BOOST_LOG_API void basic_debug_output_backend< CharT >::consume(record_view const&, string_type const& formatted_message)  
                                              ^  
libs\log\src\windows\debug_output_backend.cpp:64:16: error: 'basic_debug_output_backend' is not a class template  
 template class basic_debug_output_backend< char >;  
                ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:64:16: error: explicit instantiation of non-template type 'boost::log::v2_mt_nt6::sinks::basic_debug_output_backend'  
libs\log\src\windows\debug_output_backend.cpp:67:16: error: 'basic_debug_output_backend' is not a class template  
 template class basic_debug_output_backend< wchar_t >;  
                ^~~~~~~~~~~~~~~~~~~~~~~~~~  
libs\log\src\windows\debug_output_backend.cpp:67:16: error: explicit instantiation of non-template type 'boost::log::v2_mt_nt6::sinks::basic_debug_output_backend'  
  
    "g++"   -fvisibility-inlines-hidden -m32 -mthreads -O0 -fno-inline -Wall -g -fvisibility=hidden -march=i686 -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32 -DBOOST_USE_WINDOWS_H -DDATE_TIME_INLINE -DNOMINMAX -DSECURITY_WIN32 -DWIN32_LEAN_AND_MEAN -D__STDC_CONSTANT_MACROS  -I"." -I"libs\log\src" -c -o "__build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\debug\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o" "libs\log\src\windows\debug_output_backend.cpp"  
  
...failed gcc.compile.c++ __build-msys_gcc-32\boost\bin.v2\libs\log\build\gcc-7.4.0\debug\threadapi-win32\threading-multi\visibility-hidden\windows\debug_output_backend.o...  
```  
  
All other libraries builds without errors.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-20 17:07:23 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589194572  

Can you provide the full build log?  
  
This may have something to do with your Boost installation, as `basic_debug_output_backend` is definitely defined in `boost/log/sinks/debug_output_backend.hpp` and it is definitely a template.

---

## Comment 2

> Username: andry81  
> Created at: 2020-02-20 18:02:33 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589228166  

> Can you provide the full build log?  
  
[2020'02'20_19'42'02''353.build.log](https://github.com/boostorg/log/files/4232298/2020.02.20_19.42.02.353.build.log)

---

## Comment 3

> Username: Lastique  
> Created at: 2020-02-20 18:42:18 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589245836  

Nothing new there. Can you provide the preprocessed output of `debug_output_backend.cpp`? You can obtain it by invoking:  
  
```  
"g++"   -fvisibility-inlines-hidden -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -march=i686 -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32 -DBOOST_USE_WINDOWS_H -DDATE_TIME_INLINE -DNDEBUG -DNOMINMAX -DSECURITY_WIN32 -DWIN32_LEAN_AND_MEAN -D__STDC_CONSTANT_MACROS  -I"." -I"libs\log\src" -E -o "debug_output_backend.P" "libs\log\src\windows\debug_output_backend.cpp"  
```

---

## Comment 4

> Username: andry81  
> Created at: 2020-02-20 19:39:23 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589271438  

```  
"g++.exe"   -fvisibility-inlines-hidden -m32 -mthreads -O3 -finline-functions -Wno-inline -Wall -g -fvisibility=hidden -march=i686 -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_USE_DLL=1 -DBOOST_THREAD_WIN32 -DBOOST_USE_WINDOWS_H -DDATE_TIME_INLINE -DNDEBUG -DNOMINMAX -DSECURITY_WIN32 -DWIN32_LEAN_AND_MEAN -D__STDC_CONSTANT_MACROS  -I"." -I"libs\log\src" -c -E -o "debug_output_backend.P" "libs\log\src\windows\debug_output_backend.cpp"  
```  
[debug_output_backend.P.txt](https://github.com/boostorg/log/files/4232660/debug_output_backend.P.txt)

---

## Comment 5

> Username: Lastique  
> Created at: 2020-02-21 00:08:59 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589434513  

`debug_output_backend.hpp` is completely missing from the preprocessed output. It seems, the compiler incorrectly processes `#pragma once` in that file and assumes it has already been included. If so, this is a compiler bug. You can test that by commenting that line.

---

## Comment 6

> Username: andry81  
> Created at: 2020-02-21 01:08:58 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589450050  

This didn't help except that:  
```  
//#ifndef BOOST_LOG_WITHOUT_DEBUG_OUTPUT  
...  
//#endif // BOOST_LOG_WITHOUT_DEBUG_OUTPUT  
```

---

## Comment 7

> Username: Lastique  
> Created at: 2020-02-21 15:42:25 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589708269  

Looks like `BOOST_WINDOWS` is not defined on your platform for some reason. This leads to `BOOST_LOG_WITHOUT_DEBUG_OUTPUT` being defined in `boost/log/detail/config.hpp`, which is included in `debug_output_backend.hpp`. In `debug_output_backend.cpp`, the check for `BOOST_LOG_WITHOUT_DEBUG_OUTPUT` is made before including any headers, so its contents are not removed.  
  
I tried installing a fresh MSYS2 installation locally and can't reproduce the problem with the current Boost develop. Either the compiler was fixed or something in Boost.Config was updated. Can you try building the develop branch of Boost?

---

## Comment 8

> Username: andry81  
> Created at: 2020-02-21 22:45:47 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589868569  

Seems it fixed.

---

## Comment 9

> Username: Lastique  
> Created at: 2020-02-22 08:09:35 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589931148  

The change I made will fix the compilation, but it doesn't fix the missing `BOOST_WINDOWS` definition. As a result, the `debug_output_backend` will be unavailable while it could be supported. You should investigate why your platform is not recognized as Windows. Or upgrade your compiler, as I suspect that is what was fixed.

---

## Comment 10

> Username: andry81  
> Created at: 2020-02-22 14:29:48 UTC  
> Updated at: 2020-02-22 15:25:55 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-589961344  

1. `Boost.config` does not select Windows, it selects Cygwin which is not the Windows and does not have the `BOOST_WINDOWS` definition: `boost\config\platform\cygwin.hpp`  
But it has no `BOOST_CYGWIN` either.  
  
2. `Boost.predef` does not select Windows too, but selects the Cygwin: `boost\predef\os\cygwin.h`  
  
3. You can test it in your config: `boost/log/detail/config.hpp`  
```  
#include <boost/predef/os.h>  
  
// Try including WinAPI config as soon as possible so that any other headers don't include Windows SDK headers  
#if defined(BOOST_OS_WINDOWS_AVAILABLE)  
#include <boost/winapi/config.hpp>  
#endif  
  
#include <limits.h> // To bring in libc macros  
#include <boost/config.hpp>  
  
#define a <::BOOST_WINDOWS|BOOST_CYGWIN|BOOST_PLATFORM_CONFIG|BOOST_OS_WINDOWS|BOOST_OS_CYGWIN>  
#include a // <- here  
```  
  
```  
In file included from libs\log\src\attribute_set.cpp:16:0:  
./boost/log/detail/config.hpp:36:11: fatal error: ::BOOST_WINDOWS|BOOST_CYGWIN|"boost/config/platform/cygwin.hpp"|( (((0)%100)*10000000) + (((0)%100)*100000) + ((0)%100000) )|( (((0)%100)*10000000) + (((338)%100)*100000) + (( 0)%100000) ): No such file or directory  
 #define a <::BOOST_WINDOWS|BOOST_CYGWIN|BOOST_PLATFORM_CONFIG|BOOST_OS_WINDOWS|BOOST_OS_CYGWIN>  
```

---

## Comment 11

> Username: Lastique  
> Created at: 2020-02-23 11:38:13 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590059648  

Cygwin is indeed not Windows, and Boost.Log is correct to disable Windows-specific backends on Cygwin. However, as I understand, MSYS is not a Cygwin platform, it uses MinGW to target native Windows. On MinGW, `BOOST_WINDOWS` must be defined.

---

## Comment 12

> Username: Lastique  
> Created at: 2020-02-23 11:50:25 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590060558  

Or are you building with the non-MinGW compiler? In that case, yes, that seems to be a Cygwin target.

---

## Comment 13

> Username: andry81  
> Created at: 2020-02-23 14:56:56 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590076932  

Of cause Cygwin is not Msys which is not Mingw.  
But both does not support msys directly. Not the first nor the second (msys2).  
Even more, Msys can contain mingw components and cygwin can contain mingw components, and cygwin can contain msys components, so the `BOOST_WINDOWS` becomes useless here.  
So I don't understand what means non-MinGW compiler, it has no sence in what kind of cygwin-to-msys-to-mingw composition.  
All these means the boost does not support it properly.

---

## Comment 14

> Username: Lastique  
> Created at: 2020-02-23 15:54:03 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590083095  

MSYS (the legacy one) did not have a compiler targeting it. In other words, MSYS is not a target platform. Instead, MSYS shipped MinGW, which is a version of gcc with its own Windows SDK, which creates binaries running on native Windows. IOW, MinGW targets native Windows.  
  
Cygwin is a POSIX-like platform that builds on top of native Windows API. Cygwin does have its own set of compilers and headers, which allow to compile POSIX applications to work on top of Cygwin runtime. In other words, Cygwin is a target platform separate from Windows.  
  
You can optionally install MinGW in Cygwin, as you can do in most Linux distros, for example. Those MinGW installations still target native Windows.  
  
MSYS2 is a new project, which not only ships MinGW, but also appears to offer a Cygwin target with a separate set of compilers and headers. In a way, it is equivalent to the original Cygwin project. (At least it seems so to me.) Initially I thought you used MinGW compiler from MSYS2 as I did not know it also had a Cygwin target.  
  
`BOOST_WINDOWS` must be defined when the target is native Windows. This includes any versions of MinGW, but not Cygwin.  
  
I hope, it makes things clear.

---

## Comment 15

> Username: Lastique  
> Created at: 2020-02-23 15:57:26 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590083502  

Also, it is true MSYS2 is not explicitly supported by Boost because (a) this is a new system (for one, I've not heard of it before this report), and (b) the demand seems to be low. MSYS2 support will be by incident.

---

## Comment 16

> Username: andry81  
> Created at: 2020-02-23 16:29:38 UTC  
> Updated at: 2020-02-23 16:32:09 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590087547  

> MSYS (the legacy one) did not have a compiler targeting it.  
  
Actionally it has, I saw versions of gcc builded specifically versus msys (as I recall correctly - `msysdvlpr` or something like this) and which could print the msys keyword in the `gcc --version` output. And there is a standalone `__MSYS__` macro which introduces the msys as a target and has it's own version: https://github.com/msys2/msys2/wiki/Porting  
  
>Cygwin is a POSIX-like platform that builds on top of native Windows API.  
  
They all at some point posix and windows in the same time. All dependends on a level of integrity with the linux/windows features. Cygwin just have the most deepest level of integrity with the posix, so it brings more posix than, for example, the msys, which was a fork from cygwin at some time ago. So that is not so much important.  
  
> BOOST_WINDOWS must be defined when the target is native Windows. This includes any versions of MinGW, but not Cygwin.  
  
I think it should not be defined, because all these targets are not a pure windows native. otherwise there would be definitely a misunderstanding on how to check the target correctly:  
```  
#if BOOST_WINDOWS  
# if BOOST_MINGW  
```  
or  
```  
#if BOOST_MINGW  
# if BOOST_WINDOWS  
```  
Which one is more correct here?  
  
But this does not bring a level of complexity above:  
```  
#if BOOST_WINDOWS  
// use native headers, calls to win32 api directly  
#elseif BOOST_MINGW  
// use mingw headers, calls to mingw wrappers, if no wrappers -> calls to win32 api directly  
```

---

## Comment 17

> Username: Lastique  
> Created at: 2020-02-23 16:56:18 UTC  
> Updated at: 2020-02-23 16:58:13 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590090479  

> And there is a standalone `__MSYS__` macro which introduces the msys as a target and has it's own version: https://github.com/msys2/msys2/wiki/Porting  
  
That link is for MSYS2. I haven't seen a compiler targeting MSYS (the legacy 1.x one).  
  
> They all at some point posix and windows in the same time.  
  
Cygwin does not provide Windows SDK. MinGW does.  
  
It's pretty similar to how Win32 API builds on top of NT kernel API. AFAIR, there were a couple official POSIX APIs on top of NT API, and as I understand it, the modern UWP API also builds on top of it. Cygwin is different in that it is mostly builds on top of Win32 API.  
  
> I think it should not be defined, because all these targets are not a pure windows native. otherwise there would be definitely a misunderstanding on how to check the target correctly  
  
MinGW targets native Windows. As does MSVC and clang-cl and a few other compilers. So testing for MinGW (more precisely, `__MINGW32__` macro) is testing for a particular compiler. Granted, that compiler is only available on Windows, but that is irrelevant and coincidental.  
  
Testing for `BOOST_WINDOWS` means testing whether we're building for Windows. It doesn't say which compiler is used for building.

---

## Comment 18

> Username: andry81  
> Created at: 2020-02-23 18:22:02 UTC  
> Updated at: 2020-02-23 18:27:07 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590099759  

> > And there is a standalone `__MSYS__` macro which introduces the msys as a target and has it's own version: https://github.com/msys2/msys2/wiki/Porting  
>  
> That link is for MSYS2. I haven't seen a compiler targeting MSYS (the legacy 1.x one).  
  
For example: https://sourceforge.net/projects/mingw/files/Other/Unsupported/MSYS/msysDVLPR/  
  
> > They all at some point posix and windows in the same time.  
>   
> Cygwin does not provide Windows SDK. MinGW does.  
>   
It has it's own port. Run `cygwin.exe` from `cygwin.com` and choose: `View: Full`, `Search: w32`. There would be 2 versions of win32: `Win32 API headers for Cygwin 64bit toolchain` and `MinGW-w64 Windows API headers for Cygwin`.  
  
> MinGW targets native Windows.  
  
It could use it's own paths (like /c/blabla) and it's own api layer (`mingwm10.dll`), so it's already not fully native from that point.  
  
> Testing for `BOOST_WINDOWS` means testing whether we're building for Windows. It doesn't say which compiler is used for building.  
  
We are building not just for Windows, we are building versus headers and libraries, which in turn could be different in Mingw/Cygwin/Msys or native Windows SDK headers and libraries. So that macro is not enough to say exactly which one headers and libraries should be used.

---

## Comment 19

> Username: Lastique  
> Created at: 2020-02-23 19:51:35 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590108676  

> For example: https://sourceforge.net/projects/mingw/files/Other/Unsupported/MSYS/msysDVLPR/  
  
Ok. What system does that compiler target?  
  
> It has it's own port. Run cygwin.exe from cygwin.com and choose: View: Full, Search: w32. There would be 2 versions of win32: Win32 API headers for Cygwin 64bit toolchain and MinGW-w64 Windows API headers for Cygwin.  
  
I'm not sure what is the difference between those. But that doesn't change what I said before.  
  
> It could use it's own paths (like /c/blabla) and it's own api layer (mingwm10.dll), so it's already not fully native from that point.  
  
MinGW does not support any paths beyond what is supported by MSVC runtime and native Windows. To use POSIX-style paths you need to compile your application for a POSIX target, like Cygwin. BTW, Windows does natively support paths with forward slashes. Though not all APIs and components like drive letters still persist.  
  
mingwm10.dll is part of the compiler runtime, it does not implement any public system APIs. Yes, gcc is very different from MSVC, it cannot use the msvcrt.dll runtime and has to rely on its own. You will also find that it uses its own C++ standard library (libstdc++-6.dll) and C++ runtime (libgcc*.dll, libgomp.dll). But your application still directly calls Win32 functions like `OpenFile` and `CloseFile` from kernel32.dll.  
  
> We are building not just for Windows, we are building versus headers and libraries, which in turn could be different in Mingw/Cygwin/Msys or native Windows SDK headers and libraries. So that macro is not enough to say exactly which one headers and libraries should be used.  
  
Of course, you are free to further detect which Windows SDK and what compiler you are using. This is mostly needed to work around differences between SDKs (which is why Boost.WinAPI exists). However, regardless which Windows SDK you are using, you are targeting native Windows and you are expected to use Win32 API. Which is indicated by `BOOST_WINDOWS`.

---

## Comment 20

> Username: andry81  
> Created at: 2020-02-23 22:39:07 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590124909  

> > For example: https://sourceforge.net/projects/mingw/files/Other/Unsupported/MSYS/msysDVLPR/  
>   
> Ok. What system does that compiler target?  
  
It depends on what you mean by "target". This one has to be used to build msys itself, so the target for this is msys.  
  
> > It has it's own port. Run cygwin.exe from cygwin.com and choose: View: Full, Search: w32. There would be 2 versions of win32: Win32 API headers for Cygwin 64bit toolchain and MinGW-w64 Windows API headers for Cygwin.  
>   
> I'm not sure what is the difference between those. But that doesn't change what I said before.  
  
These are different ported headers, which compiles without headers from the native Windows SDK, so, not related to it.  
  
> > We are building not just for Windows, we are building versus headers and libraries, which in turn could be different in Mingw/Cygwin/Msys or native Windows SDK headers and libraries. So that macro is not enough to say exactly which one headers and libraries should be used.  
>   
> Of course, you are free to further detect which Windows SDK and what compiler you are using. This is mostly needed to work around differences between SDKs (which is why Boost.WinAPI exists). However, regardless which Windows SDK you are using, you are targeting native Windows and you are expected to use Win32 API. Which is indicated by `BOOST_WINDOWS`.  
  
Then what definition to use to indication native Windows without mingw/cygwin/msys or whatever else?

---

## Comment 21

> Username: Lastique  
> Created at: 2020-02-24 08:14:29 UTC  
> Url: https://github.com/boostorg/log/issues/102#issuecomment-590207312  

> It depends on what you mean by "target". This one has to be used to build msys itself, so the target for this is msys.  
  
Since MSYS runs on native Windows then I assume it is the target. Then the compiler are likely equivalent to MinGW and have been superseded by it, hence the deprecation.  
  
> Then what definition to use to indication native Windows without mingw/cygwin/msys or whatever else?  
  
Whatever compiler-specific macro there is. For example, `BOOST_MSVC`, `BOOST_INTEL_WIN`, `BOOST_CLANG`.
