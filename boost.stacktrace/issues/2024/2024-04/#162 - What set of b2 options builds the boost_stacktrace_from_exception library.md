# #162 - What set of b2 options builds the boost_stacktrace_from_exception library? [Closed]

> Username: bgemmill  
> Created at: 2024-04-19 14:27:05 UTC  
> Updated at: 2024-07-16 05:43:14 UTC  
> Closed at: 2024-07-16 05:43:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162  

If I download boost 1.85, and go into stacktrace and do a build:  
```  
cd libs/stacktrace/build  
../../../b2 'define=BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/11/include/backtrace.h>' boost_stacktrace_from_exception  
Performing configuration checks  
  
    - default address-model    : none (cached) [1]  
    - default architecture     : none (cached) [1]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - libbacktrace builds      : no  (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [4]  
    - libbacktrace builds      : no  (cached) [4]  
  
[1] clang-18  
[2] clang-linux-18/debug/python-3.10/visibility-hidden  
[3] clang-linux-18/debug/link-static/python-3.10/visibility-hidden  
[4] clang-linux-18/debug/build-no/python-3.10/visibility-hidden  
...found 1 target...  
```  
  
libbacktrace isn't found and the boost_stacktrace_from_exception library won't be built.   
  
Is there a better set of options to use here to build stacktrace?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-04-26 15:53:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2079660905  

Add `-d+2` to the command line to get more information about the build.  
  
For example:  
```  
boost/libs/stacktrace/build$ ../../../b2 'define=BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/11/include/backtrace.h>' boost_stacktrace_from_exception -d+2  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - symlinks supported       : yes (cached)  
    - cxx11_rvalue_references  : yes [2]  
    - cxx11_rvalue_references  : yes [2]  
  
[1] gcc-9  
[2] gcc-9/debug/threadapi-pthread/visibility-hidden  
gcc.compile.c++ ../../../bin.v2/libs/stacktrace/build/gcc-9/debug/visibility-hidden/from_exception.o  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/11/include/backtrace.h>  -I"../../.."  -c -o "../../../bin.v2/libs/stacktrace/build/gcc-9/debug/visibility-hidden/from_exception.o" "../../../libs/stacktrace/build/../src/from_exception.cpp"  
  
/bin/sh: 2: cannot open /usr/lib/gcc/x86_64-linux-gnu/11/include/backtrace.h: No such file  
```  
  
What is the output in your case?

---

## Comment 2

> Username: bgemmill  
> Created at: 2024-04-30 14:49:19 UTC  
> Updated at: 2024-04-30 14:50:54 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2085551505  

```  
$ ../../../b2 'define=BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/13/include/backtrace.h>' -d+2 boost_stacktrace_from_exception  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - cxx11_rvalue_references  : yes (cached) [3]  
    - libbacktrace builds      : no  (cached) [2]  
    - cxx11_rvalue_references  : yes (cached) [4]  
    - libbacktrace builds      : no  (cached) [4]  
    - addr2line builds         : yes (cached) [2]  
    - addr2line builds         : yes (cached) [2]  
    - WinDbg builds            : no  (cached) [2]  
    - WinDbg builds            : no  (cached) [2]  
    - WinDbg builds            : no  (cached) [4]  
    - WinDbgCached builds      : no  (cached) [2]  
    - WinDbgCached builds      : no  (cached) [4]  
  
[1] clang-18  
[2] clang-linux-18/debug/python-3.12/visibility-hidden  
[3] clang-linux-18/debug/link-static/python-3.12/visibility-hidden  
[4] clang-linux-18/debug/build-no/python-3.12/visibility-hidden  
```  
  
edit: copied the wrong output the first time.  
  
Am I definining the location of backtrace correctly for b2 here?

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-05-27 08:48:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2132980311  

LGTM

---

## Comment 4

> Username: apolukhin  
> Created at: 2024-05-27 08:50:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2132984134  

Add a `-a` along with `-d+2` option, so that the cached values are recomputed. Attach the build log to this issue

---

## Comment 5

> Username: bgemmill  
> Created at: 2024-05-28 13:41:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2135250815  

Thanks for getting back to me. I see:  
  
```  
$ ../../../b2 -d+2 -a 'define=BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/13/include/backtrace.h>' boost_stacktrace_from_exception  
Performing configuration checks  
  
    - default address-model    : none [1]  
    - default architecture     : none [1]  
    - cxx11_rvalue_references  : yes [2]  
    - cxx11_rvalue_references  : yes [3]  
    - libbacktrace builds      : no [2]  
    - cxx11_rvalue_references  : yes [4]  
    - libbacktrace builds      : no [4]  
  
[1] clang-18  
[2] clang-linux-18/debug/python-3.12/visibility-hidden  
[3] clang-linux-18/debug/link-static/python-3.12/visibility-hidden  
[4] clang-linux-18/debug/build-no/python-3.12/visibility-hidden  
  
```  
  
This appears to output a 0 byte file called `-DTEST_BOOST_NO_CXX11_RVALUE_REFERENCES`.  
  
Are there other build logs I should be looking for?

---

## Comment 6

> Username: apolukhin  
> Created at: 2024-06-10 19:27:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/162#issuecomment-2159132809  

Changed the behavior in upstream in https://github.com/boostorg/stacktrace/commit/2c835636951eaf84069ce76f922615069f0ddc39  
  
Just provide an explicit `boost.stacktrace.from_exception=on` option and the library should build... or output a readable error message
