# #218 Fix build for Android API < 21 [Closed]

> Username: ruslo  
> Created at: 2018-04-22 21:11:12 UTC  
> Updated at: 2018-04-22 22:08:38 UTC  
> Closed at: 2018-04-22 21:45:01 UTC  
> Url: https://github.com/boostorg/thread/pull/218  

Disable BOOST_THREAD_INTERNAL_CLOCK_IS_MONO for Android API < 21 to  
avoid usage of pthread_condattr_setclock

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2018-04-22 21:38:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/218#pullrequestreview-114215867  

📁 include/boost/thread/detail/config.hpp

```diff
 428 |-     #define BOOST_THREAD_HAS_MONO_CLOCK
 429 |-     #define BOOST_THREAD_INTERNAL_CLOCK_IS_MONO
 428 |+     #if defined(__ANDROID__) && defined(__ANDROID_API__) && (__ANDROID_API__ < 21)
```

> Username: viboes  
> Created_at: 2018-04-22 21:38:11 UTC  
> Updated_at: 2018-04-22 21:38:12 UTC  
> Url: https://github.com/boostorg/thread/pull/218#discussion_r183252465  

It is weird, we have already a check for __ANDROID__ in line 420.


---

## Comment 2

> Username: ruslo  
> Created_at: 2018-04-22 21:45:00 UTC  
> Url: https://github.com/boostorg/thread/pull/218#issuecomment-383414808  

Okay, it seems to be fixed recently:  
* https://github.com/boostorg/thread/commit/2ef70e02a5406132dd47bacfc342549e6518f3aa  
  
@gjasny Thanks :)

---
