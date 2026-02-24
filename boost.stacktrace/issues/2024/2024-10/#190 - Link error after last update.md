# #190 - Link error after last update [Closed]

> Username: pdimov  
> Created at: 2024-10-16 17:21:36 UTC  
> Updated at: 2024-10-17 08:57:55 UTC  
> Closed at: 2024-10-17 08:57:54 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/190  

```  
[ 86%] Linking CXX executable ../../../stage/bin/boost_stacktrace-test  
/usr/bin/ld: ../../../stage/lib/libboost_stacktrace_from_exception.a(from_exception.cpp.o): in function `boost::stacktrace::detail::dump(int, void const* const*, unsigned long)':  
from_exception.cpp:(.text+0x0): multiple definition of `boost::stacktrace::detail::dump(int, void const* const*, unsigned long)'; ../../../stage/lib/libboost_stacktrace_backtrace.a(backtrace.cpp.o):backtrace.cpp:(.text+0x0): first defined here  
/usr/bin/ld: ../../../stage/lib/libboost_stacktrace_from_exception.a(from_exception.cpp.o): in function `boost::stacktrace::detail::dump(char const*, void const* const*, unsigned long)':  
from_exception.cpp:(.text+0x30): multiple definition of `boost::stacktrace::detail::dump(char const*, void const* const*, unsigned long)'; ../../../stage/lib/libboost_stacktrace_backtrace.a(backtrace.cpp.o):backtrace.cpp:(.text+0x30): first defined here  
/usr/bin/ld: ../../../stage/lib/libboost_stacktrace_from_exception.a(from_exception.cpp.o): in function `boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned long, unsigned long)':  
from_exception.cpp:(.text+0xa0): multiple definition of `boost::stacktrace::detail::this_thread_frames::collect(void const**, unsigned long, unsigned long)'; ../../../stage/lib/libboost_stacktrace_backtrace.a(backtrace.cpp.o):backtrace.cpp:(.text+0xa0): first defined here  
```  
https://github.com/boostorg/boost/actions/runs/11365925203/job/31615153789

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-10-17 08:57:54 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/190#issuecomment-2418960228  

Thanks, fixed in https://github.com/boostorg/stacktrace/commit/088bf8ed7f4a6ca59c96bdb54cf88f1a0dda2ebf
