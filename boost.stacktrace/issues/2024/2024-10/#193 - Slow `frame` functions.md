# #193 - Slow `frame` functions [Closed]

> Username: HazyMrf  
> Created at: 2024-10-24 17:34:21 UTC  
> Updated at: 2025-11-08 11:45:11 UTC  
> Closed at: 2025-11-08 11:45:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193  

Hello! I use boost stacktrace library and `frame` functions like `name()` are taking too much time (like +-10seconds) probably because of the large binary (about 35MB). Is there any way I can speed up these functions?   
  
SetUp  
Compiler: clang-18   
Flags: -DBOOST_STACKTRACE_USE_BACKTRACE -DBOOST_STACKTRACE_LINK

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-10-26 10:06:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193#issuecomment-2439471950  

What flag do you use for compiling your application? Are you using SSD? What operating system do you use?

---

## Comment 2

> Username: HazyMrf  
> Created at: 2024-10-30 11:28:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193#issuecomment-2446717134  

Thank you @apolukhin for such a quick response!  
Flags: -fno-pie -fvisibility-inlines-hidden -O3 -funroll-loops -fno-omit-frame-pointer -flto=thin   
-DNDEBUG -DBOOST_STACKTRACE_USE_BACKTRACE -DBOOST_STACKTRACE_LINK  
SSD is not used  
OS is Ubuntu20

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-12-22 13:35:30 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193#issuecomment-2558459356  

@HazyMrf 35mb does seem to be big enough to drastically slow down the `name()` function.  
  
My guesses are:  
0. Try preload all the internal staff by calling `to_string(boost::stacktrace::stacktrace())`. Does it help the subsequent calls?  
1. You are throwing exceptions or getting other stacktraces concurrently. Ubuntu20 has an old libc that locks+unlocks mutex on each frame unwinding. As a result multiple concurrent exceptions|backtraces could degrade performance to multiple seconds per one function call. The most simple solution - is to migrate to a new distro.  
2. Try defining BOOST_STACKTRACE_BACKTRACE_FORCE_STATIC while compiling the boost.stacktrace library. Does it help?

---

## Comment 4

> Username: HazyMrf  
> Created at: 2025-01-09 10:18:26 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193#issuecomment-2579708986  

Nope, any of the things you suggested didn't help :( Well I'll continue to use `address()` because it is extremely cheap. I've always thought that addr2line is slow and when using backtrace / libbacktrace I'll get super-performance, but I was mistaken...

---

## Comment 5

> Username: apolukhin  
> Created at: 2025-11-08 11:45:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/193#issuecomment-3506474762  

Can not reproduce the problem. Works fine with Ubuntu 20.04- Ubuntu 24.04 on a multi-gigabyte binariees, tested on https://github.com/userver-framework/userver/ project, on hundreds of services
