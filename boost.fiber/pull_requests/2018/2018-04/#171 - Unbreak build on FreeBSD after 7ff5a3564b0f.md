# #171 Unbreak build on FreeBSD after 7ff5a3564b0f [Merged]

> Username: jbeich  
> Created at: 2018-04-10 16:42:12 UTC  
> Updated at: 2018-05-04 05:53:03 UTC  
> Merged at: 2018-05-04 05:52:58 UTC  
> Closed at: 2018-05-04 05:52:58 UTC  
> Url: https://github.com/boostorg/fiber/pull/171  

`pthread_t` is opaque and cannot be used with `CPU_WHICH_TID`. Looking at Linux implementation `pthread_setaffinity_np()` should work on FreeBSD as well.  
  
CC @juikim  
  
```diff  
--- src/numa/linux/pin_thread.cpp  
+++ src/numa/freebsd/pin_thread.cpp  
@@ -8,7 +8,7 @@  
   
 extern "C" {  
 #include <pthread.h>  
-#include <sched.h>  
+#include <pthread_np.h>  
 }  
   
 #include <system_error>  
@@ -28,7 +28,7 @@ void pin_thread( std::uint32_t cpuid) {  
   
 BOOST_FIBERS_DECL  
 void pin_thread( std::uint32_t cpuid, std::thread::native_handle_type h) {  
-    cpu_set_t set;  
+    cpuset_t set;  
     CPU_ZERO( & set);  
     CPU_SET( cpuid, & set);  
     int err = 0;  
  
```

---

## Comment 1

> Username: juikim  
> Created_at: 2018-04-10 18:08:30 UTC  
> Url: https://github.com/boostorg/fiber/pull/171#issuecomment-380195512  

LGTM

---

## Comment 2

> Username: olk  
> Created_at: 2018-05-04 05:53:03 UTC  
> Url: https://github.com/boostorg/fiber/pull/171#issuecomment-386511264  

ty

---
