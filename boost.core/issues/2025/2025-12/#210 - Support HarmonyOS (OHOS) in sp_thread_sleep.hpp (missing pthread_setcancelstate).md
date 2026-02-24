# #210 - Support HarmonyOS (OHOS) in sp_thread_sleep.hpp (missing pthread_setcancelstate) [Closed]

> Username: riken01  
> Created at: 2025-12-18 08:16:08 UTC  
> Updated at: 2025-12-18 08:28:32 UTC  
> Closed at: 2025-12-18 08:28:32 UTC  
> Url: https://github.com/boostorg/core/issues/210  

**Description**   
  
When compiling Boost for HarmonyOS (OHOS) using the HarmonyOS SDK/NDK, the compilation fails in boost/core/detail/sp_thread_sleep.hpp because pthread_setcancelstate is not implemented in the HarmonyOS C library (similar to the situation on Android).  
  
**Environment**  
  
Boost Version: 1.86.0  
  
OS: HarmonyOS  
  
Compiler: Clang (HarmonyOS NDK)  
  
**Suggested Fix**   
  
The file boost/core/detail/sp_thread_sleep.hpp already contains a workaround for __ANDROID__. This should be extended to support __OHOS__ as well.  
  
// Proposed change around line 59, 70 and 88  
#if defined(BOOST_HAS_THREADS) && !defined(__ANDROID__) && !defined(__OHOS__)  
This change allows Boost Core to skip the pthread_setcancelstate call on HarmonyOS, ensuring compatibility with its POSIX implementation.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-18 08:28:32 UTC  
> Url: https://github.com/boostorg/core/issues/210#issuecomment-3669024180  

This has been fixed since Boost 1.88 by https://github.com/boostorg/core/pull/187.
