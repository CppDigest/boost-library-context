# #215 - Android < 21 must not use pthread_condattr_setclock [Closed]

> Username: gjasny  
> Created at: 2018-04-11 17:19:42 UTC  
> Updated at: 2019-08-15 05:35:39 UTC  
> Closed at: 2018-04-12 07:06:52 UTC  
> Url: https://github.com/boostorg/thread/issues/215  

Hello,  
  
the `pthread_condattr_setclock` function is only available for `__ANDROID_API__ >= 21`.  The unconditional use in [pthread_helpers.hpp](https://github.com/boostorg/thread/blob/develop/include/boost/thread/pthread/pthread_helpers.hpp#L28) leads to an compile error.  
  
What would be the proper way to handle it? Should the preprocessor condition in `boost::pthread::cond_init` be tightened or `BOOST_THREAD_INTERNAL_CLOCK_IS_MONO` not be defined on Android < 21?  
  
Would the following patch make sense?  
  
```diff  
diff --git a/boost/thread/detail/config.hpp b/boost/thread/detail/config.hpp  
index 70c74687f6..aae25268d3 100644  
--- a/boost/thread/detail/config.hpp  
+++ b/boost/thread/detail/config.hpp  
@@ -417,6 +417,11 @@  
   #define BOOST_THREAD_INTERNAL_CLOCK_IS_MONO  
 #elif defined(BOOST_THREAD_CHRONO_MAC_API)  
   #define BOOST_THREAD_HAS_MONO_CLOCK  
+#elif defined(__ANDROID__)  
+  #define BOOST_THREAD_HAS_MONO_CLOCK  
+  #if defined(__ANDROID_API__) && __ANDROID_API__ >= 21  
+    #define BOOST_THREAD_INTERNAL_CLOCK_IS_MONO  
+  #endif  
 #else  
   #include <time.h> // check for CLOCK_MONOTONIC  
   #if defined(CLOCK_MONOTONIC)  
```  
  
Thanks,  
Gregor

---

## Comment 1

> Username: viboes  
> Created at: 2018-04-11 21:18:50 UTC  
> Url: https://github.com/boostorg/thread/issues/215#issuecomment-380598939  

Hi,  
  
for completion, could you add the compiler error?  
  
Yes, please, could you provide a PR?

---

## Comment 2

> Username: gjasny  
> Created at: 2018-04-12 06:49:55 UTC  
> Url: https://github.com/boostorg/thread/issues/215#issuecomment-380697238  

Compile error:  
  
```  
build	11-Apr-2018 07:46:57	clang-linux.compile.c++.without-pth /home/bamboo/bamboo-agent-home/xml-data/build-dir/EL-BOOST7-AC/_output/Applications_Android_armeabi-v7a_cxx14/boost/bin.v2/libs/type_erasure/build/clang-linux-android/debug/link-static/pch-off/target-os-android/threadapi-pthread/threading-multi/dynamic_binding.o  
build	11-Apr-2018 07:46:57	In file included from libs/type_erasure/src/dynamic_binding.cpp:14:  
build	11-Apr-2018 07:46:57	In file included from ./boost/thread/shared_mutex.hpp:28:  
build	11-Apr-2018 07:46:57	In file included from ./boost/thread/pthread/shared_mutex.hpp:14:  
build	11-Apr-2018 07:46:57	In file included from ./boost/thread/mutex.hpp:16:  
build	11-Apr-2018 07:46:57	In file included from ./boost/thread/pthread/mutex.hpp:26:  
build	11-Apr-2018 07:46:57	./boost/thread/pthread/pthread_helpers.hpp:28:15: error: use of undeclared identifier 'pthread_condattr_setclock'; did you mean 'pthread_condattr_setpshared'?  
build	11-Apr-2018 07:46:57	              pthread_condattr_setclock(&attr, CLOCK_MONOTONIC);  
build	11-Apr-2018 07:46:57	              ^~~~~~~~~~~~~~~~~~~~~~~~~  
build	11-Apr-2018 07:46:57	              pthread_condattr_setpshared  
build	11-Apr-2018 07:46:57	/home/bamboo/bamboo-agent-home/xml-data/build-dir/EL-BOOST7-AC/_toolchain_arm_android-14/bin/../sysroot/usr/include/pthread.h:122:5: note: 'pthread_condattr_setpshared' declared here  
build	11-Apr-2018 07:46:57	int pthread_condattr_setpshared(pthread_condattr_t* __attr, int __shared);  
build	11-Apr-2018 07:46:57	    ^  
build	11-Apr-2018 07:46:57	1 error generated.  
```  
  
In `pthread.h`:  
```C  
#if __ANDROID_API__ >= 21  
int pthread_condattr_setclock(pthread_condattr_t* __attr, clockid_t __clock) __INTRODUCED_IN(21);  
#endif /* __ANDROID_API__ >= 21 */  
```

---

## Comment 3

> Username: huowa222  
> Created at: 2019-08-15 05:35:39 UTC  
> Url: https://github.com/boostorg/thread/issues/215#issuecomment-521520254  

in chromium build, there is a build config for android called config.gni  
try to get it and refresh the value of android api level.
