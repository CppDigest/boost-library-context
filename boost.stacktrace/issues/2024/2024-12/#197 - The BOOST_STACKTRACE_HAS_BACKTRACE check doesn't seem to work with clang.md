# #197 - The BOOST_STACKTRACE_HAS_BACKTRACE check doesn't seem to work with clang [Open]

> Username: bgemmill  
> Created at: 2024-12-20 03:42:37 UTC  
> Updated at: 2025-01-04 10:09:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/197  

When configuring cmake on ubuntu 24.04 with clang, the check for libbacktrace doesn't seem to work, even when libbacktrace is installed in the default location.  
  
For reference, gcc puts libbacktrace here:  
`/usr/lib/gcc/x86_64-linux-gnu/13/include/backtrace.h`  
  
Configuring with clang, we see:  
```  
[build] -- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE  
[build] -- Performing Test BOOST_STACKTRACE_HAS_BACKTRACE - Failed  
```  
  
This can be corrected by explicitly telling the library where the header resides (the extra escapes are to pass the literal quotes all the way in to the header file as a define):  
```  
-DCMAKE_CXX_FLAGS="-DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=\\\"/usr/lib/gcc/x86_64-linux-gnu/13/include/backtrace.h\\\""  
```  
  
Can the cmake check of BOOST_STACKTRACE_HAS_BACKTRACE be modified to look at the gcc install location?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-12-20 03:57:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/197#issuecomment-2556237348  

Even if the check is modified to look somewhere not in the include path, the library would still not compile without passing `-DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE`:  
  
https://github.com/boostorg/stacktrace/blob/22982db3f6630de16434150d181e0da07ce76d74/include/boost/stacktrace/detail/libbacktrace_impls.hpp#L20-L24  
  
so what would be the point of modifying the check?  
  
Does CMake's FindBacktrace module work on this configuration?  
  
https://cmake.org/cmake/help/latest/module/FindBacktrace.html  
  
Maybe the library needs to use that.

---

## Comment 2

> Username: bgemmill  
> Created at: 2025-01-03 18:27:02 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/197#issuecomment-2569646304  

CMake's FindBacktrace does work, but detects [this backtrace](https://man7.org/linux/man-pages/man3/backtrace.3.html):  
```  
[cmake] -- Backtrace_HEADER execinfo.h  
[cmake] -- Backtrace_INCLUDE_DIR /usr/include  
```  
  
Rather than the [desired backtrace](https://github.com/gcc-mirror/gcc/blob/master/libbacktrace/backtrace.h), and that looks like a different implementation.   
  
@apolukhin does the backtrace in `execinfo.h` correspond to any of [these](https://www.boost.org/doc/libs/develop/doc/html/stacktrace/configuration_and_build.html)?

---

## Comment 3

> Username: apolukhin  
> Created at: 2025-01-04 10:09:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/197#issuecomment-2571113015  

> [@apolukhin](https://github.com/apolukhin) does the backtrace in `execinfo.h` correspond to any of [these](https://www.boost.org/doc/libs/develop/doc/html/stacktrace/configuration_and_build.html)?  
  
Unfortunately no. It is an inconvenient interface that uses the desired functions under the hood
