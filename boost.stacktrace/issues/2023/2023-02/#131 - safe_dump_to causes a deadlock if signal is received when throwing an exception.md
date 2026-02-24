# #131 - safe_dump_to causes a deadlock if signal is received when throwing an exception [Closed]

> Username: tworonix  
> Created at: 2023-02-05 21:01:35 UTC  
> Updated at: 2024-03-20 10:07:49 UTC  
> Closed at: 2024-03-20 10:07:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/131  

### Steps to reproduce  
1. Install a timer using standard Posix `timer_create` and `SIGEV_SIGNAL`  
2. Call `safe_dump_to` in the signal handler  
3. Throw C++ exceptions in the main code  
  
**Actual behavior:**  
The process deadlocks and hangs forever.  
  
When investigating this issue I found out that throwing an exception in C++ involves locking a mutex (see here for more details: https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2544r0.html )  
  
Apparently both throwing the exception and collecting the stacktrace from the signal handler try to lock the same mutex. This leads to a deadlock, or possibly also other undefined behaviour.  
  
Unfortunately this makes `safe_dump_to` not so safe to call from a signal handler, which I find very bad news. I wish there was a way to get it to work reliably. I was using quite old versions, but I don't think this was fixed in any newer releases of gcc or boost?  
  
### Platform details  
boost-1.66, Linux Intel x64, gcc-8.2.1, library compiled with  BOOST_STACKTRACE_USE_ADDR2LINE  
  
### Output of gdb after loading the coredump of the deadlocked process  
```  
#0  0x00007fc0bf18c4ed in __lll_lock_wait () from /lib64/libpthread.so.0  
#1  0x00007fc0bf187dcb in _L_lock_883 () from /lib64/libpthread.so.0  
#2  0x00007fc0bf187c98 in pthread_mutex_lock () from /lib64/libpthread.so.0  
#3  0x00007fc0bdcfca2a in _Unwind_Find_FDE () from /lib64/libgcc_s.so.1  
#4  0x00007fc0bdcf9d2c in ?? () from /lib64/libgcc_s.so.1  
#5  0x00007fc0bdcfa6ed in ?? () from /lib64/libgcc_s.so.1  
#6  0x00007fc0bdcfaf88 in _Unwind_Backtrace () from /lib64/libgcc_s.so.1  
#7  0x00000000004ee1bd in boost::stacktrace::detail::this_thread_frames::collect (out_frames=0xfdf180 <Contoso::sDump>, max_frames_count=<optimized out>, skip=<optimized out>) at /opt/Contoso/boost/dts9/1.66.0/include/boost/stacktrace/detail/collect_unwind.ipp:59  
#8  0x00000000004ee16c in boost::stacktrace::detail::this_thread_frames::safe_dump_to_impl (memory=0xfdf180 <Contoso::sDump>, size=<optimized out>, skip=<optimized out>) at /opt/Contoso/boost/dts9/1.66.0/include/boost/stacktrace/safe_dump_to.hpp:55  
#9  0x00000000004ed492 in boost::stacktrace::safe_dump_to (memory=0x7fc0bdf00360, size=256) at /opt/Contoso/boost/dts9/1.66.0/include/boost/stacktrace/safe_dump_to.hpp:90  
#10 Contoso::(anonymous namespace)::HandleTimerSignal (signum=<optimized out>) at /opt/contoso/build-dir/source/libs/stacktrace/stack_trace.cc:24  
#11 <signal handler called>  
#12 0x00007fc0bf188e1b in pthread_mutex_unlock () from /lib64/libpthread.so.0  
#13 0x00007fc0bdcfca7a in _Unwind_Find_FDE () from /lib64/libgcc_s.so.1  
#14 0x00007fc0bdcf9d2c in ?? () from /lib64/libgcc_s.so.1  
#15 0x00007fc0bdcfabd3 in _Unwind_RaiseException () from /lib64/libgcc_s.so.1  
#16 0x00007fc0be261986 in __cxa_throw () from /lib64/libstdc++.so.6  
#17 0x0000000000508820 in Contoso::ThrowingFunction   
```

---

## Comment 1

> Username: itrofimow  
> Created at: 2024-03-02 02:32:19 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/131#issuecomment-1974216417  

> I was using quite old versions, but I don't think this was fixed in any newer releases of gcc or boost?  
  
This doesn't really have much to do with boost, since the global lock in question comes from `dl_iterate_phdr` glibc function, which unwinders invoke when doing their stuff.  
  
However, starting from glibc2.35 and gcc-12.3/llvm-16.0.0 this is no longer the case (at least it shouldn't be):  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=71744#c32  
https://reviews.llvm.org/D130668

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-03-04 08:20:59 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/131#issuecomment-1975977024  

Adding more clarification in #156   
  
As @itrofimow noted, there's not much we can do in Boost.Stacktrace.

---

## Comment 3

> Username: apolukhin  
> Created at: 2024-03-20 10:07:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/131#issuecomment-2009188357  

Many thanks to all the participants! The docs are updated, changes will be in Boost 1.85
