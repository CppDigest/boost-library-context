# #161 - Using futex(2) on OpenBSD [Closed]

> Username: brad0  
> Created at: 2021-09-07 09:01:18 UTC  
> Updated at: 2021-09-16 20:05:32 UTC  
> Closed at: 2021-09-16 20:05:32 UTC  
> Url: https://github.com/boostorg/log/issues/161  

I wanted to eventually submit a PR to enable the use of futex(2) on NetBSD and OpenBSD but I am not sure  
how to approach that with the code as is and wanted some feedback. Initially I had the following but didn't  
think you would like me using BOOST_LOG_USE_ATOMIC.  
  
```  
diff --git a/include/boost/log/detail/config.hpp b/include/boost/log/detail/config.hpp  
index 84b194e..f1a9f9a 100644  
--- a/include/boost/log/detail/config.hpp  
+++ b/include/boost/log/detail/config.hpp  
@@ -321,6 +321,11 @@  
 #endif  
 #endif  
  
+// futex  
+#if defined(BOOST_OS_LINUX) || defined(BOOST_OS_BSD_OPEN)  
+#define BOOST_LOG_USE_ATOMIC  
+#endif  
+  
 namespace boost {  
  
 // Setup namespace name  
diff --git a/include/boost/log/detail/event.hpp b/include/boost/log/detail/event.hpp  
index 0168e17..1546b52 100644  
--- a/include/boost/log/detail/event.hpp  
+++ b/include/boost/log/detail/event.hpp  
@@ -23,7 +23,7 @@  
  
 #if defined(BOOST_THREAD_PLATFORM_PTHREAD)  
 #   include <boost/atomic/capabilities.hpp>  
-#   if (defined(linux) || defined(__linux) || defined(__linux__)) && BOOST_ATOMIC_INT_LOCK_FREE == 2  
+#   if defined(BOOST_LOG_USE_ATOMIC) && BOOST_ATOMIC_INT_LOCK_FREE == 2  
 #       include <boost/atomic/atomic.hpp>  
 #       define BOOST_LOG_EVENT_USE_FUTEX  
 #   elif defined(_POSIX_SEMAPHORES) && (_POSIX_SEMAPHORES + 0) > 0 && BOOST_ATOMIC_FLAG_LOCK_FREE == 2  
diff --git a/src/event.cpp b/src/event.cpp  
index f576648..12acbb3 100644  
--- a/src/event.cpp  
+++ b/src/event.cpp  
@@ -28,9 +28,14 @@  
 #include <stddef.h>  
 #include <errno.h>  
 #include <sys/syscall.h>  
-#include <linux/futex.h>  
 #include <boost/memory_order.hpp>  
  
+#if defined (BOOST_OS_BSD_OPEN)  
+#include <sys/futex.h>  
+#else  
+#include <linux/futex.h>  
+#endif  
+  
 // Some Android NDKs (Google NDK and older Crystax.NET NDK versions) don't define SYS_futex  
 #if defined(SYS_futex)  
 #define BOOST_LOG_SYS_FUTEX SYS_futex  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-09-07 10:40:13 UTC  
> Url: https://github.com/boostorg/log/issues/161#issuecomment-914195297  

Reusing futex support in Boost.Atomic is a good idea, especially since Boost.Atomic also supports other similar APIs. But the patch needs to be substantially different. The implementation should just use `boost::atomic` internally and not have to deal with `futex` directly. The change should also be localized to the event component.  
  
I will work on it when the time permits. Thanks for the suggestion.

---

## Comment 2

> Username: brad0  
> Created at: 2021-09-07 10:45:53 UTC  
> Url: https://github.com/boostorg/log/issues/161#issuecomment-914198490  

Thank you. I welcome any diffs you come up with.
