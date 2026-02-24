# #169 - Something is wrong with the configuration macros when static linking [Closed]

> Username: pdimov  
> Created at: 2024-06-21 15:29:52 UTC  
> Updated at: 2024-06-23 19:17:14 UTC  
> Closed at: 2024-06-23 19:17:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/169  

When using static libraries, a variety of things don't work. On macOS, any attempted use fails with  
```  
In file included from /Users/runner/work/stacktrace/boost-root/libs/stacktrace/test/cmake_install_test/main.cpp:6:  
In file included from /Users/runner/.local/include/boost/stacktrace/stacktrace.hpp:27:  
In file included from /Users/runner/.local/include/boost/stacktrace/safe_dump_to.hpp:[22](https://github.com/boostorg/stacktrace/actions/runs/9615275190/job/26522048559#step:8:23)1:  
/Users/runner/.local/include/boost/stacktrace/detail/collect_unwind.ipp:33:2: error: "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
#error "Boost.Stacktrace requires `_Unwind_Backtrace` function. Define `_GNU_SOURCE` macro or `BOOST_STACKTRACE_GNU_SOURCE_NOT_REQUIRED` if _Unwind_Backtrace is available without `_GNU_SOURCE`."  
 ^  
```  
whereas shared libraries work. Under Linux and Windows, things "work", but for instance `test_noop.cpp` fails when linked with `boost_stacktrace_noop` statically, even though it succeeds when linked dynamically.  
  
I believe this is caused by the library incorrectly setting its configuration macros. `safe_dump_to.hpp` includes the implementation `.ipp` files when BOOST_STACKTRACE_LINK is not defined:  
  
https://github.com/boostorg/stacktrace/blob/8b79199a2a36e5659a0a7d4bfc4062e4ddf88b16/include/boost/stacktrace/safe_dump_to.hpp#L208  
  
BOOST_STACKTRACE_LINK is a nonstandard macro that is set in `push_options.h` when the standard one BOOST_STACKTRACE_DYN_LINK is defined:  
  
https://github.com/boostorg/stacktrace/blob/8b79199a2a36e5659a0a7d4bfc4062e4ddf88b16/include/boost/stacktrace/detail/push_options.h#L11  
  
It is, however, not set when building static libraries (whose standard configuration macro is BOOST_STACKTRACE_STATIC_LINK, which CMakeLists.txt sets.)  
  
Consequently, the library "thinks" it's in header only mode when static libraries are used, which is wrong and causes the issues above.  
  
I don't know what the correct fix is; maybe BOOST_STACKTRACE_LINK needs to be defined when BOOST_STACKTRACE_STATIC_LINK is set, maybe something else. But currently things aren't right. (I think that this also presents problems on MinGW and Cygwin.)
