# #75 - Build Failure with Boost 1.68 Using GCC 8.2.1 and MinGW-w64 (Windows, _WIN32_WINNT=0xA00) [Closed]

> Username: MrM21632  
> Created at: 2018-11-25 15:04:52 UTC  
> Updated at: 2018-11-25 17:20:37 UTC  
> Closed at: 2018-11-25 17:20:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/75  

I'm experiencing build failures with Boost 1.68 using the latest SVN/Git revisions of GCC 8.2.1 and MinGW-w64. I have set the default _WIN32_WINNT for MinGW-w64 to 0xA00 (i.e., Windows 10), and am attempting to build through the latest version of MSYS2.  
  
I'm getting several errors similar to this one when building:  
  
```  
gcc.compile.c++ bin.v2\libs\log\build\gcc-8.2.1\release\link-static\runtime-link-static\threadapi-win32\threading-multi\setup\init_from_settings.o  
In file included from ./boost/thread/win32/thread_primitives.hpp:31,  
                 from ./boost/thread/win32/thread_heap_alloc.hpp:9,  
                 from ./boost/thread/detail/thread_heap_alloc.hpp:15,  
                 from ./boost/thread/tss.hpp:10,  
                 from ./boost/log/sinks/basic_sink_frontend.hpp:30,  
                 from ./boost/log/sinks/unlocked_frontend.hpp:26,  
                 from ./boost/log/sinks.hpp:22,  
                 from libs\log\src\setup\init_from_settings.cpp:54:  
./boost/winapi/handles.hpp:49:9: error: '::CompareObjectHandles' has not been declared  
 using ::CompareObjectHandles;  
         ^~~~~~~~~~~~~~~~~~~~  
  
```  
  
It seems like either there's an issue with how CompareObjectHandles is declared in this header file, or there's an issue with MinGW-w64 that needs addressing.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-25 17:20:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/75#issuecomment-441456574  

Given that the error appears when Boost.Log is compiled, I'm assuming `BOOST_USE_WINDOWS_H` is defined. Which means that `CompareObjectHandles` declaration is missing in MinGW-w64 Platform SDK headers. MinGW-w64 probably does not support Windows 10 API, so my suggestion is to report this problem to MinGW-w64 devs and use a lower value for `_WIN32_WINNT` (or define `BOOST_USE_WINAPI_VERSION` to a lower version to just build Boost for a lower Windows version).
