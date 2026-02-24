# #73 - wait ops backend problem in MinGW [Closed]

> Username: joaquintides  
> Created at: 2025-05-30 09:58:01 UTC  
> Updated at: 2025-05-30 10:22:01 UTC  
> Closed at: 2025-05-30 10:07:12 UTC  
> Url: https://github.com/boostorg/atomic/issues/73  

Recent changes cause previously working Boost.Unordered tests to fail for MinGW:  
  
https://github.com/boostorg/unordered/actions/runs/15342165860/job/43170510013  
```  
2025-05-30T08:30:51.6146204Z gcc.compile.c++ bin.v2\libs\container\build\gcc-15\release\x86_32\cxxstd-11-iso\inlining-on\optimization-space\threadapi-win32\threading-multi\visibility-hidden\dlmalloc.o  
2025-05-30T08:30:52.6784912Z gcc.compile.c++ bin.v2\libs\thread\build\gcc-15\release\x86_32\cxxstd-11-iso\inlining-on\optimization-space\threadapi-win32\threading-multi\visibility-hidden\win32\thread_primitives.o  
2025-05-30T08:30:52.6921456Z In file included from ./boost/atomic/detail/wait_operations.hpp:21,  
2025-05-30T08:30:52.6950353Z                  from ./boost/atomic/detail/atomic_impl.hpp:28,  
2025-05-30T08:30:52.7224372Z                  from ./boost/atomic/atomic.hpp:25,  
2025-05-30T08:30:52.7257064Z                  from C:/actions-runner/_work/unordered/boost-root/libs/thread/src/win32/thread_primitives.cpp:19:  
2025-05-30T08:30:52.7778036Z ./boost/atomic/detail/wait_ops_windows.hpp: In static member function 'static boost::atomics::detail::wait_operations_windows<Base, Size>::storage_type boost::atomics::detail::wait_operations_windows<Base, Size>::wait(const volatile storage_type&, storage_type, boost::memory_order)':  
2025-05-30T08:30:52.7947713Z ./boost/atomic/detail/wait_ops_windows.hpp:62:28: error: 'WaitOnAddress' is not a member of 'boost::winapi'; did you mean 'WaitOnAddress'? [-Wtemplate-body]  
2025-05-30T08:30:52.8192945Z    62 |             boost::winapi::WaitOnAddress(const_cast< storage_type* >(&storage), &old_val, Size, boost::winapi::infinite);  
```  
  
I'm sorry I can't pinpoint to where the exact problem lies.

---

## Comment 1

> Username: Lastique  
> Created at: 2025-05-30 10:07:12 UTC  
> Updated at: 2025-05-30 10:22:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/73#issuecomment-2921892554  

Boost.Atomic now requires Windows 10 or later. You need to define `_WIN32_WINNT=0x0A00` on MinGW-w64. I think, Windows SDK headers on MinGW-w64 define it to some older version, so if it gets included in some indirect way the default value in Boost.WinAPI no longer applies. I don't see a way around it, sorry.  
  
Update: Yes, `sdkddkver.h` defines `_WIN32_WINNT` to `_WIN32_WINNT_WS03` (0x0502).
