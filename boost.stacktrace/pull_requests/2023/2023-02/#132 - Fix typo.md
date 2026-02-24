# #132 Fix typo [Merged]

> Username: Chocobo1  
> Created at: 2023-02-08 09:32:43 UTC  
> Updated at: 2023-02-21 19:23:30 UTC  
> Merged at: 2023-02-21 11:27:08 UTC  
> Closed at: 2023-02-21 11:27:08 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/132  



---

## Review 1 [Commented]

> Username: Chocobo1  
> Created_at: 2023-02-08 09:43:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/stacktrace/pull/132#pullrequestreview-1288809400  

📁 doc/stacktrace.qbk

```diff
 291 |     [[*BOOST_STACKTRACE_USE_WINDBG_CACHED*] [*boost_stacktrace_windbg_cached*] [ Uses `dbgeng.h` to show debug info and caches internals in TLS for better performance. Useful only for cases when traces are gathered very often. May require linking with *ole32* and *dbgeng*. ] [MSVC, Intel on Windows] [yes] [no]]
 292 |-     [[*BOOST_STACKTRACE_USE_BACKTRACE*] [*boost_stacktrace_backtrace*] [Requires linking with *libdl* on POSIX and *libbacktrace* libraries[footnote Some *libbacktrace* packages SEGFAULT if there's a concurrent work with the same `backtrace_state` instance. To avoid that issue the Boost.Stacktrace library uses `thread_local` states, unfortuantely this may consume a lot of memory if you often create and destroy execution threads in your application. Define *BOOST_STACKTRACE_BACKTRACE_FORCE_STATIC* to force single instance, but make sure that [@https://github.com/boostorg/stacktrace/blob/develop/test/thread_safety_checking.cpp thread_safety_checking.cpp] works well in your setup. ]. *libbacktrace* is probably already installed in your system[footnote If you are using Clang with libstdc++ you could get into troubles of including `<backtrace.h>`, because on some platforms Clang does not search for headers in the GCC's include paths and any attempt to add GCC's include path leads to linker errors. To explicitly specify a path to the `<backtrace.h>` header you could define the *BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE* to a full path to the header. For example on Ubuntu Xenial use the command line option *-DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/5/include/backtrace.h>* while building with Clang. ], or built into your compiler.
 292 |+     [[*BOOST_STACKTRACE_USE_BACKTRACE*] [*boost_stacktrace_backtrace*] [Requires linking with *libdl* on POSIX and *libbacktrace* libraries[footnote Some *libbacktrace* packages SEGFAULT if there's a concurrent work with the same `backtrace_state` instance. To avoid that issue the Boost.Stacktrace library uses `thread_local` states, unfortunately this may consume a lot of memory if you often create and destroy execution threads in your application. Define *BOOST_STACKTRACE_BACKTRACE_FORCE_STATIC* to force single instance, but make sure that [@https://github.com/boostorg/stacktrace/blob/develop/test/thread_safety_checking.cpp thread_safety_checking.cpp] works well in your setup. ]. *libbacktrace* is probably already installed in your system[footnote If you are using Clang with libstdc++ you could get into troubles of including `<backtrace.h>`, because on some platforms Clang does not search for headers in the GCC's include paths and any attempt to add GCC's include path leads to linker errors. To explicitly specify a path to the `<backtrace.h>` header you could define the *BOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE* to a full path to the header. For example on Ubuntu Xenial use the command line option *-DBOOST_STACKTRACE_BACKTRACE_INCLUDE_FILE=</usr/lib/gcc/x86_64-linux-gnu/5/include/backtrace.h>* while building with Clang. ], or built into your compiler.
```

> Username: Chocobo1  
> Created_at: 2023-02-08 09:43:40 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/132#discussion_r1099887932  

unfortuantely -> unfortunately


---

## Comment 2

> Username: Chocobo1  
> Created_at: 2023-02-12 04:40:06 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/132#issuecomment-1426941469  

@apolukhin   
Ping!

---

## Comment 3

> Username: apolukhin  
> Created_at: 2023-02-21 11:27:32 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/132#issuecomment-1438317448  

Many thanks for the fixes!

---

## Comment 4

> Username: coveralls  
> Created_at: 2023-02-21 19:23:30 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/132#issuecomment-1438981163  

[![Coverage Status](https://coveralls.io/builds/57125815/badge)](https://coveralls.io/builds/57125815)  
  
Coverage: 93.84%. Remained the same when pulling **b9a0a12f1c57f10aa65b1ee5584bfb3f94bdaa27 on Chocobo1:typo** into **4cf47389c1f3c870641e698a1e9325dae89878ed on boostorg:develop**.

---
