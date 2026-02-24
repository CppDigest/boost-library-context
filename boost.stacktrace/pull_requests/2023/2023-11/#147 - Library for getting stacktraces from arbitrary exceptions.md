# #147 Library for getting stacktraces from arbitrary exceptions [Merged]

> Username: apolukhin  
> Created at: 2023-11-22 17:18:26 UTC  
> Updated at: 2024-02-14 18:40:21 UTC  
> Merged at: 2024-02-07 08:27:45 UTC  
> Closed at: 2024-02-07 08:27:45 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147  



---

## Comment 1

> Username: coveralls  
> Created_at: 2023-12-02 11:59:58 UTC  
> Updated_at: 2024-02-04 12:39:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1837131347  

[![Coverage Status](https://coveralls.io/builds/65485687/badge)](https://coveralls.io/builds/65485687)  
  
coverage: 92.462% (-0.1%) from 92.573%  
when pulling **9f4de7e1546af2d248264478ab7af4adfe13989c on from-exception**  
into **3de5aea55430f88f53e9d7dd49e939b01547a744 on develop**.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-02-07 17:42:32 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1932561318  

This doesn't build on macOS  
```  
clang-darwin.compile.c++ bin.v2/libs/stacktrace/build/clang-darwin-13/release/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/from_exception.o  
In file included from libs/stacktrace/build/../src/from_exception.cpp:10:  
./boost/stacktrace/safe_dump_to.hpp:37:31: error: no type named 'size_t' in namespace 'std'; did you mean simply 'size_t'?  
    BOOST_STACKTRACE_FUNCTION std::size_t from_dump(const char* filename, native_frame_ptr_t* out_frames);  
                              ^~~~~~~~~~~  
                              size_t  
```  
and Windows  
```  
compile-c-c++ bin.v2\libs\stacktrace\build\msvc-14.2\release\link-static\threading-multi\from_exception.obj  
from_exception.cpp  
.\boost/stacktrace/safe_dump_to.hpp(37): error C2039: 'size_t': is not a member of 'std'  
C:\Program Files (x86)\Windows Kits\10\include\10.0.22621.0\ucrt\stddef.h(23): note: see declaration of 'std'  
```  
(https://github.com/boostorg/boost/actions/runs/7812285701/job/21309026607)  
  
Your CI seems incomplete. :-)

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-02-12 15:16:57 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1938873987  

Now that this has been merged to master, it breaks the master build of Boost as well.  
  
https://github.com/boostorg/boost/actions/runs/7863976420

---

## Comment 4

> Username: apolukhin  
> Created_at: 2024-02-12 19:12:30 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1939369950  

Ouch. Fixing that...

---

## Comment 5

> Username: apolukhin  
> Created_at: 2024-02-13 08:24:38 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1940720536  

Should be fixed in develop and master now

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-02-13 17:42:37 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1942085489  

Isn't, although the errors are different this time. :-)  
  
https://github.com/boostorg/boost/actions/runs/7876917683  
  
```  
clang-darwin.compile.c++ bin.v2/libs/stacktrace/build/clang-darwin-13/release/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/from_exception.o  
In file included from libs/stacktrace/build/../src/from_exception.cpp:10:  
In file included from ./boost/stacktrace/safe_dump_to.hpp:219:  
./boost/stacktrace/detail/collect_unwind.ipp:33:2: error: "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
#error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 ^  
1 error generated.  
```  
on macOS, and  
```  
compile-c-c++ bin.v2\libs\stacktrace\build\msvc-14.2\release\link-static\threading-multi\from_exception.obj  
from_exception.cpp  
libs\stacktrace\build\..\src\from_exception.cpp(13): fatal error C1083: Cannot open include file: 'dlfcn.h': No such file or directory  
```  
on Windows.  
  
As I said you need Mac/Windows CI. :-)

---

## Comment 7

> Username: apolukhin  
> Created_at: 2024-02-14 18:15:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1944354844  

There is a windows CI :)  
  
https://github.com/boostorg/stacktrace/blob/f6d4e117cdfd4f08e5d534b7025afdfb15e9a0ae/.github/workflows/ci.yml#L109-L121  
  
The failure is about something different. The library should not build at all at Windows, but it does. Looks like some error in my Jamfile, some `b2` commands trigger the build

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-02-14 18:24:50 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1944370947  

I see nothing in the build/Jamfile that would prevent the library from building on Windows.

---

## Comment 9

> Username: apolukhin  
> Created_at: 2024-02-14 18:37:41 UTC  
> Updated_at: 2024-02-14 18:40:21 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/147#issuecomment-1944388676  

Oh, it does not build in tests because the tests for that particular library are disabled on Windows. However, that does not prevents from building the library itself.  
  
My fault, fix is coming...

---
