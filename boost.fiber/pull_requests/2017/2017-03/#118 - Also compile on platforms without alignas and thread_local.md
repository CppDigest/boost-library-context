# #118 Also compile on platforms without alignas and thread_local [Closed]

> Username: kaihowl  
> Created at: 2017-03-27 08:59:09 UTC  
> Updated at: 2021-03-01 06:55:41 UTC  
> Closed at: 2021-03-01 06:55:41 UTC  
> Url: https://github.com/boostorg/fiber/pull/118  

I created this "workaround" to also get Boost Fiber working on iOS and QNX builds. These changes will very likely affect the performance on those platforms and invalidate the current benchmarks. It should have no effect on the platforms which already supported thread_local and alignas.  
  
iOS does not support the thread_local keyword at all. Even in the latest XCode version when it was introduced for the OSX platform.  
  
QNX uses a gcc 4.7 compiler which has no thread_local support but general C++11 support.

---

## Comment 1

> Username: olk  
> Created_at: 2017-03-27 11:50:11 UTC  
> Url: https://github.com/boostorg/fiber/pull/118#issuecomment-289430667  

could you prepare your patches for branch develop please

---

## Comment 2

> Username: kaihowl  
> Created_at: 2017-03-28 04:41:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/118#issuecomment-289661134  

I have not yet looked into changes necessary to the boost build system: For the fallback of thread_local there is a newly introduced dependency on Boost.Thread. Compiling this locally with and without the fallback works fine. Yet, there is probably an explicit dependency on Boost.Thread missing. Let me know what you require from me to get this merged.

---

## Review 3 [Changes requested]

> Username: glenfe  
> Created_at: 2017-07-17 11:17:41 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/fiber/pull/118#pullrequestreview-50290232  

📁 include/boost/fiber/detail/config.hpp

```diff
  67 |+ #  define cacheline_length CACHELINE_LENGTH
  68 |+ #  define BOOST_FIBER_ALIGNAS_BEGIN(alignment)
  69 |+ #  define BOOST_FIBER_ALIGNAS_END(alignment) __attribute__((aligned (alignment)))
```

> Username: glenfe  
> Created_at: 2017-07-17 11:16:32 UTC  
> Updated_at: 2017-07-17 11:17:41 UTC  
> Url: https://github.com/boostorg/fiber/pull/118#discussion_r127682244  

I don't think you should just assume that if no C++11 alignas that __attribute__aligned is available. i.e. These kinds of implementation specific definitions should be within the detection for the right implementation, e.g. #if defined(BOOST_GCC) && (BOOST_GCC > version)  
  
But specifically for this case, when no C++11 alignas is available, why can't you just use BOOST_ALIGNMENT (provided by Boost.Config) which has meaningful definitions for multiple C++ implementations?

> Username: keryell  
> Created_at: 2021-02-19 22:02:43 UTC  
> Url: https://github.com/boostorg/fiber/pull/118#discussion_r579501611  

On the other hands Boost.Fiber requires at least C++11, a 10-year old version of C++...  
Does this still matter?


---
