# #412 Add monotonic time support with np `pthread_cond_timedwait` for Android and Apple [Open]

> Username: vvydria  
> Created at: 2025-02-17 20:51:11 UTC  
> Updated at: 2025-02-17 20:55:11 UTC  
> Url: https://github.com/boostorg/thread/pull/412  

Improvements.  
  
Android and Apple support none-portable functions for monotonic pthread cond wait.  
   
Apple supports `pthread_cond_timedwait_relative_np` since macos(10.4) and ios(2.0): https://github.com/apple/darwin-libpthread/blob/main/include/pthread/pthread.h#L532  
  
Android up to `__ANDROID_API__ < 21` supports `pthread_cond_timedwait_monotonic_np` and `pthread_cond_timedwait_relative_np`.  
  
Note that in some versions of the NDK, these symbols were removed from the header file(https://android.googlesource.com/platform/bionic/+/refs/heads/ndk-r13-release/libc/include/pthread.h) although they were present in the libc.map( https://android.googlesource.com/platform/bionic/+/refs/heads/ndk-r13-release/libc/libc.map.txt#779) and cpp(https://android.googlesource.com/platform/bionic/+/refs/heads/ndk-r13-release/libc/bionic/pthread_cond.cpp#221).  
  
A workaround is to declare these functions as follows:  
`extern "C" int pthread_cond_timedwait_[monotonic|relative]_np(pthread_cond_t*, pthread_mutex_t*, const struct timespec*);`  
  
See related comments on this: https://android.googlesource.com/platform/bionic.git/+/refs/heads/ndk-release-r16/libc/include/pthread.h#162  
  
Thank you !

---
