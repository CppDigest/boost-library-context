# #166 Switching the Windows condition_variable and interruptible_wait functions over to using internal_timespec_clock. [Merged]

> Username: austin-beer  
> Created at: 2017-09-28 19:42:25 UTC  
> Updated at: 2017-09-29 05:05:21 UTC  
> Merged at: 2017-09-28 23:56:59 UTC  
> Closed at: 2017-09-28 23:56:59 UTC  
> Url: https://github.com/boostorg/thread/pull/166  

I ended up getting to this sooner than I'd planned.  
  
Here are the results on Windows with this PR.  
  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_austin_no_generic_shared.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_austin_yes_generic_shared.txt

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 20:55:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/166#pullrequestreview-65996798  

📁 include/boost/thread/pthread/timespec.hpp

```diff
 334 |+       return mono_timespec_timepoint(static_cast<boost::intmax_t>(ns));
 335 |+ #else
 336 |+       // Use GetTickCount64() because it's more reliable on older
```

> Username: viboes  
> Created_at: 2017-09-28 20:55:15 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141733765  

Does it means that Boost.Chrono should use this on those systems?

> Username: austin-beer  
> Created_at: 2017-09-28 21:18:00 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141738871  

Yes, that is correct. Boost Chrono should probably provide an option so users can use GetTickCount64() instead of QueryPerformanceCounter() when using chrono::steady_time on older systems. Right now the only option those users have is to avoid using chrono::steady_time on those systems.

> Username: viboes  
> Created_at: 2017-09-28 21:38:44 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141743099  

Issue added https://github.com/boostorg/chrono/issues/28

> Username: MarcelRaad  
> Created_at: 2017-09-29 05:05:21 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141790548  

That would be great! To be exact, it's the GetTickCount64 emulation that works best on those systems. Unfortunately, GetTickCount64 itself has also only been introduced in Windows Vista.


---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-28 21:00:59 UTC  
> Url: https://github.com/boostorg/thread/pull/166#issuecomment-332962734  

Wow, this is a really big change and a big simplification of the code :)  
I would need some time to digest all these change, in particular every thing concerning the windows changes.  
  
I don't know why AppVeyor CI has not been launched on this PR.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-28 21:02:37 UTC  
> Url: https://github.com/boostorg/thread/pull/166#issuecomment-332963197  

I found the AppVeyor link  
https://ci.appveyor.com/project/viboes/thread/build/1.0.93-feature/timespec_clocks  
  
Everything is Ok.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-09-28 21:27:12 UTC  
> Url: https://github.com/boostorg/thread/pull/166#issuecomment-332968815  

The windows report is impressive.   
* No more no-timeout,   
* generic shared_mutex and   
* future   
  
are all ok :)  
  
Only failing  
* some timed_mutex  
* some recursive_timed_mutex and  
* some shared_mutex  
  
I'll not have too much time before the C++ standard meeting mailing list deadline (16 Oct). I will really appreciate if you can work on this.  
  
BTW, I'm looking for a co-maintainer for Boost.Thread ;-)

---

## Review 5 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 21:32:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/166#pullrequestreview-66006150  

📁 include/boost/thread/detail/config.hpp

```diff
 394 |+   #if defined(CLOCK_MONOTONIC)
 395 |+     #define BOOST_THREAD_HAS_MONO_CLOCK
 396 |+     #if defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC)
```

> Username: viboes  
> Created_at: 2017-09-28 21:32:18 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141741817  

We need a way to detect this. We need a program that succeeds if everything is there and works as expected (Something like https://github.com/boostorg/thread/blob/develop/build/has_atomic_flag_lockfree_test.cpp).   
  
Then we can use it as in  
  
https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L154  
  
and  
  
https://github.com/boostorg/thread/blob/develop/build/Jamfile.v2#L280


---

## Review 6 [Commented]

> Username: viboes  
> Created_at: 2017-09-28 21:33:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/166#pullrequestreview-66006484  

📁 include/boost/thread/future.hpp

```diff
  41 | 
  42 |- #if defined(BOOST_THREAD_PLATFORM_PTHREAD)
  42 | #include <boost/thread/pthread/timespec.hpp>
```

> Username: viboes  
> Created_at: 2017-09-28 21:33:47 UTC  
> Url: https://github.com/boostorg/thread/pull/166#discussion_r141742102  

We need to move this to detail


---
