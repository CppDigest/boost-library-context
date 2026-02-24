# #141 Fix Issues with BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC [Closed]

> Username: austin-beer  
> Created at: 2017-08-31 17:44:46 UTC  
> Updated at: 2017-09-19 21:39:32 UTC  
> Closed at: 2017-09-19 21:39:32 UTC  
> Url: https://github.com/boostorg/thread/pull/141  

* Fixed https://svn.boost.org/trac10/ticket/12727 (maybe - not tested)  
* Fixed the following functions in the pthread implementation to behave correctly when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled and the system time changes:  
    * boost::this_thread::sleep_until() with a system clock time  
    * boost::thread::try_join_until() with a system clock time  
    * boost::condition_variable::wait_until() with a system clock time and predicate  
    * boost::condition_variable_any::wait_until() with a system clock time and predicate  
    * boost::shared_mutex::try_lock_until() with a system clock time  
    * boost::upgrade_mutex::try_lock_until() with a system clock time  
    * boost::timed_mutex::try_lock_for()  
    * boost::timed_mutex::try_lock_until()  
    * boost::recursive_timed_mutex::try_lock_for()  
    * boost::recursive_timed_mutex::try_lock_until()  
* Fixed the following deprecated functions in the pthread implementation to behave correctly (some would hang) when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled:  
    * boost::this_thread::sleep()  
    * boost::thread::timed_join()  
    * boost::condition_variable::timed_wait()  
    * boost::condition_variable_any::timed_wait()  
    * boost::shared_mutex::timed_lock()  
    * boost::upgrade_mutex::timed_lock()  
    * boost::timed_mutex::timed_lock()  
    * boost::recursive_timed_mutex::timed_lock()  
* Fixed boost::condition_variable_any::timed_wait() in the pthread implementation to correctly handle is_pos_infinity() and is_special() durations.

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-08-31 17:55:09 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326373930  

I realize these changes appear fairly extensive, but I believe they are warranted in order to ensure that BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC behaves consistently and reliably regardless of which time-related functions users call.  
  
My hope is that, once these changes have been well-tested, BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC can be replaced by CLOCK_MONOTONIC so that all users can benefit (by default) from the monotonic clock whenever it is available.  
  
Here is the executable that I've been using to test these updates. It executes each function and then changes the system time while the function is executing and then measures how long the function took to actually execute.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1268037/test_boost_sleep.cpp.txt)  
  
Austin Beer

---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-01 06:37:04 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326502287  

Hi, thanks for your interest and the hard work you have done.  
I recognize that I have had a lot of trouble with the time since I started to try to improve it with monotonic clocks.  
  
I will not accept your PR as such as it introduce too much changes.   
  
I would prefer to do it in small steps if your prefer.  
  
Once of my major troubles have been to use the same timespec.  
I wonder if it is not better to wrap timespec in two classes:   
  
* mono_timespec  
* real_timespec  
  
and define the needed operations on these classes, now, plus, minus, ...  
  
and then have conversions from  Chrono types and DateTime types to timespec types  
  
* to_mono_timespec  
* to_real_timespec  
  
mono_timespec will not exist in systems that don't have MONOTONIC clocks.  
  
The other possible error I did is to convert between monotonic and real times. As we can not convert between steady clocks and system clocks, we can not convert between monotonic and real timespec.   
  
BTW, is the time provided by DateTime monotonic (steady) or real(system).  
If it provides system time we cannot convert it to monotonic.  
  
Once this is clear we could start to build on top of these clocks.  
  
Please, let me know what do you think and thanks for your interest.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-01 06:59:18 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326505641  

I'll create a branch to do all the changes needed to work with monotonic timespec, so that we can check on various platforms before going to trunk.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-09-01 07:07:51 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326507030  

Wondering if in addition I'm not mixing some times timespec that convey relative or absolute times :(  
  
So we will need   
* absolute_mono_timespec   
* absolute_real_timespec   
* relative_timespec

---

## Comment 5

> Username: austin-beer  
> Created_at: 2017-09-01 19:14:02 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326662342  

Hi Vicente,  
  
Thanks for taking the time to consider this pull request. I did wonder if the extent of the changes would be an issue. Unfortunately in order to fully support monotonic time I believe all of the changes will ultimately need to be implemented. However, these changes could certainly be broken up to a series of smaller commits so that each commit is easier to review and manage.  
  
I agree that it would be helpful to wrap the timespecs in classes so that it's clear what the timespec is representing. In addition to the three you already mention, we probably also need an "absolute_internal_timespec" class that is monotonic if the monotonic clock is enabled and realtime if it is not. Functions like condition_variable::do_wait_until() would need to be passed this type because the type of timespec these functions need depends on whether or not the monotonic clock is enabled.  
  
I think it is good to be able to convert between monotonic and realtime clocks, provided it is clear that that's what we are doing and that the conversion happens only once, when the user first calls a function and passes a time to us.  
  
I made the assumption that the DateTime times were always system/realtime, since as far as I can tell the Boost.DateTime library doesn't have a concept of a monotonic clock.  
  
I like the idea of having a branch to work with and test on various platforms. Please let me know how I can help.  
  
Austin

---

## Review 6 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 21:06:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60242443  

📁 include/boost/thread/detail/thread.hpp

```diff
 542 |-           struct timespec const ts=detail::to_timespec(abs_time);
 543 |-           return do_try_join_until(ts);
 532 |+           return do_try_join_until(detail::timespec_to_internal_clock(abs_time));
```

> Username: viboes  
> Created_at: 2017-09-01 21:06:09 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136665457  

I don't think we can convert system_time to internal_clock when internal_clock is steady_clock, as system_time is based on system_clock.  
  
In this case, we should iterate as done in line 495

> Username: austin-beer  
> Created_at: 2017-09-01 21:14:47 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136666661  

I believe the timespec_to_internal_clock function handles this case. When the monotonic clock is enabled, it takes the difference between the abs_time and the current system time, and then adds that to the current steady clock, and that's what it returns. That ensures that we wait for the correct amount of time, even if the system clock jumps while we're waiting.

> Username: viboes  
> Created_at: 2017-09-02 00:56:02 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685330  

Ok, I see for timespec_to_internal_clock. The name is not a good one.   
  
I don't think the user wants to wait a delay, otherwise it would had used a duration.  
He want to join until a specific system_time. If the time is changed while joining, either the time would already be reached and then we need to return or the time is not reached yet, and then we need to iterate.   
  
The current implementation is not good neither.


---

## Review 7 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 21:42:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60248178  

📁 include/boost/thread/detail/thread.hpp

```diff
 549 |         {
 563 |-             return timed_join(get_system_time()+rel_time);
 550 |+             return do_try_join_until(detail::timespec_plus_internal_clock(rel_time));
```

> Username: viboes  
> Created_at: 2017-09-01 21:42:31 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136670529  

This has a sense only on Pthread. Windows has no timespec.

> Username: austin-beer  
> Created_at: 2017-09-01 21:49:44 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136671552  

Agreed. This particular instance is in an #else section that's only enabled when BOOST_THREAD_PLATFORM_WIN32 is not defined.

> Username: viboes  
> Created_at: 2017-09-02 00:45:02 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685015  

RIght :)


---

## Review 8 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 21:43:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60248250  

📁 include/boost/thread/detail/thread.hpp

```diff
 551 |-           timespec ts = boost::detail::to_timespec(d);
 552 |-           return do_try_join_until(ts);
 539 |+           return do_try_join_until(boost::detail::to_timespec(tp.time_since_epoch()));
```

> Username: viboes  
> Created_at: 2017-09-01 21:43:02 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136670591  

This has a sense only on Pthread. Windows has no timespec.

> Username: austin-beer  
> Created_at: 2017-09-01 21:50:26 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136671654  

Agreed. This particular instance is in an #else section that's only enabled when BOOST_THREAD_PLATFORM_WIN32 is not defined.

> Username: viboes  
> Created_at: 2017-09-02 00:44:55 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685010  

RIght :)


---

## Review 9 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 21:44:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60248474  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 171 | 
 172 |+ #ifdef BOOST_THREAD_USES_CHRONO
 173 |+ #ifdef BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC
```

> Username: viboes  
> Created_at: 2017-09-01 21:44:41 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136670870  

This type should be declared only once. What about moving it to a specific file?

> Username: austin-beer  
> Created_at: 2017-09-01 21:45:34 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136670997  

That's a good idea.

> Username: viboes  
> Created_at: 2017-09-01 22:41:06 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677118  

What about thread/detail/internal_clock.hpp?


---

## Review 10 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 21:52:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60249644  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 237 |-             }
 238 |-             return true;
 263 |+             return do_wait_until(m,detail::timespec_to_internal_clock(abs_time),pred);
```

> Username: viboes  
> Created_at: 2017-09-01 21:52:39 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136671943  

system_time and internal_clock can not be converted when the internal clock is steady

> Username: austin-beer  
> Created_at: 2017-09-01 21:56:29 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136672491  

I believe the timespec_to_internal_clock function handles this case. When the monotonic clock is enabled, it takes the difference between the abs_time and the current system time, and then adds that to the current steady clock, and that's what it returns. That ensures that we wait for the correct amount of time, even if the system clock jumps while we're waiting.


---

## Review 11 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:12:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60252164  

📁 include/boost/thread/pthread/mutex.hpp

```diff
  39 |+ // CLOCK_MONOTONIC only works with pthread_cond_timedwait(), not with pthread_mutex_timedlock()
  40 |+ #ifdef BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC
  41 |+ #undef BOOST_PTHREAD_HAS_TIMEDLOCK
```

> Username: viboes  
> Created_at: 2017-09-01 22:12:15 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136674254  

We can not undef this here. This is a global define. If Boost.Thread can not use pthread_mutex_timedlock() when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is defined, this should stay internal to Boost.Thread

> Username: austin-beer  
> Created_at: 2017-09-01 22:13:33 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136674399  

Ah, good to know. I'll have to fix that.

> Username: viboes  
> Created_at: 2017-09-02 07:46:14 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691402  

I believe it is enough to check in addition  is defined or not BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC or define a new specific BOOST_THREAD_MACRO.


---

## Review 12 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:14:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60252463  

📁 include/boost/thread/pthread/mutex.hpp

```diff
 195 |             }
 196 | #ifndef BOOST_PTHREAD_HAS_TIMEDLOCK
 197 |+ #ifdef BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC
```

> Username: viboes  
> Created_at: 2017-09-01 22:14:35 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136674506  

As this would appear several times, I believe it will be word defining an internal thread_detail::pthread_cond_init function that do this

> Username: austin-beer  
> Created_at: 2017-09-01 22:17:43 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136674833  

Good idea.


---

## Review 13 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:22:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60253369  

📁 include/boost/thread/v2/thread.hpp

```diff
  71 |       {
  72 |-         steady_clock::time_point c_timeout = steady_clock::now() + ceil<nanoseconds>(d);
  72 |+ #ifdef BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC
```

> Username: viboes  
> Created_at: 2017-09-01 22:22:44 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136675335  

Why not use internal_clock here?

> Username: austin-beer  
> Created_at: 2017-09-01 22:26:32 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136675743  

Didn't think to, but that's a good idea.


---

## Review 14 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:34:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60254691  

📁 include/boost/thread/detail/thread.hpp

```diff
 489 |         {
 490 |-           return try_join_until(chrono::steady_clock::now() + rel_time);
 490 |+           return try_join_until(thread_detail::internal_clock_t::now() + rel_time);
```

> Username: viboes  
> Created_at: 2017-09-01 22:34:24 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136676516  

Agreed.


---

## Review 15 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:36:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60254947  

📁 include/boost/thread/detail/thread.hpp

```diff
 517 |         {
 528 |-           chrono::milliseconds rel_time= chrono::ceil<chrono::milliseconds>(tp-chrono::system_clock::now());
 518 |+           chrono::milliseconds rel_time= chrono::ceil<chrono::milliseconds>(tp-thread_detail::internal_clock_t::now());
```

> Username: viboes  
> Created_at: 2017-09-01 22:36:45 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136676750  

Agreed


---

## Review 16 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:39:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255228  

📁 include/boost/thread/detail/thread.hpp

```diff
 511 |-           typedef time_point<thread_detail::internal_clock_t, nanoseconds> nano_sys_tmpt;
 512 |-           return try_join_until(nano_sys_tmpt(ceil<nanoseconds>(t.time_since_epoch())));
 498 |+           thread_detail::internal_clock_t::time_point     s_now = thread_detail::internal_clock_t::now();
```

> Username: viboes  
> Created_at: 2017-09-01 22:39:20 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136676996  

I don't agree here. You are converting from Clock to internal_clock.

> Username: austin-beer  
> Created_at: 2017-09-01 22:45:44 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677538  

I guess I'm not sure where the issue is. The code is taking the difference between t and Clock::now(), adding that difference to internal_clock_t::now(), and then passing the result to try_join_until(). Later on in this file, try_join_until() converts the internal clock time to a timespec and passes it to the pthreads implementation of do_try_join_until(), which expects an internal clock time.

> Username: viboes  
> Created_at: 2017-09-02 00:32:33 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136684542  

Oh, I see. I suspect that we need to iterate until no_timeout or Clock::now() >= tp, isn't it?

> Username: austin-beer  
> Created_at: 2017-09-02 00:44:01 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136684981  

The reason I took out and avoided code that loops until Clock::now() >= tp is because, if Clock::now() is the system clock and the system clock jumps, we could end up timing out at the wrong time.  
  
My thought is that, even if someone passes us a system_clock time, if we have access to steady_clock (because BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled), we should convert from system_clock to steady_clock and then use steady_clock under the covers.  
  
The following doesn't provide a 100% reliable conversion from a system_clock time to a steady_clock time, but I think it's better than not converting to steady_clock and then sleeping or timing out at the wrong time due to a system clock jump. This is the conversion that I use throughout the code.  
  
steady_clock_time = ( system_clock_time - system_clock::now() ) + steady_clock::now()

> Username: viboes  
> Created_at: 2017-09-02 07:43:44 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691388  

I agree with the conversion. But I don't agree with the result of the function. The result should depend on whether the system timepoint has been reached or not.


---

## Review 17 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:42:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255513  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 215 |-             struct timespec const timeout=detail::to_timespec(abs_time);
 216 |-             return do_wait_until(m, timeout);
 237 |+             return do_wait_until(m,detail::timespec_to_internal_clock(abs_time));
```

> Username: viboes  
> Created_at: 2017-09-01 22:42:06 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677217  

I don't agree here. system_time and internal_clock are not convertible

> Username: austin-beer  
> Created_at: 2017-09-02 00:48:22 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685140  

See my comments in detail/thread.hpp.


---

## Review 18 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:42:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255544  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 247 |         {
 227 |-             return timed_wait(m,get_system_time()+wait_duration);
 248 |+             if (wait_duration.is_pos_infinity())
```

> Username: viboes  
> Created_at: 2017-09-01 22:42:24 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677239  

Agreed.


---

## Review 19 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:43:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255648  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 274 |         {
 250 |-             return timed_wait(m,get_system_time()+wait_duration,pred);
 275 |+             if (wait_duration.is_pos_infinity())
```

> Username: viboes  
> Created_at: 2017-09-01 22:43:30 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677320  

Agreed


---

## Review 20 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:44:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255743  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 295 |                 lock_type& lock,
 260 |-                 const chrono::time_point<chrono::system_clock, Duration>& t)
 296 |+                 const chrono::time_point<internal_clock_t, Duration>& t)
```

> Username: viboes  
> Created_at: 2017-09-01 22:44:18 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677423  

Agreed


---

## Review 21 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:45:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255858  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 308 |           using namespace chrono;
 277 |-           system_clock::time_point     s_now = system_clock::now();
 309 |+           internal_clock_t::time_point s_now = internal_clock_t::now();
```

> Username: viboes  
> Created_at: 2017-09-01 22:45:27 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677513  

Agreed


---

## Review 22 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:46:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60255973  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 294 |-                                                    cv_status::timeout;
 295 |- 
 320 |+           return wait_until(lock, internal_clock_t::now() + d);
```

> Username: viboes  
> Created_at: 2017-09-01 22:46:34 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136677608  

Agreed


---

## Review 23 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:52:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60256492  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 279 |-           wait_until(lock, s_now + ceil<nanoseconds>(t - c_now));
 280 |-           return Clock::now() < t ? cv_status::no_timeout : cv_status::timeout;
 311 |+           return wait_until(lock, s_now + ceil<nanoseconds>(t - c_now));
```

> Username: viboes  
> Created_at: 2017-09-01 22:52:21 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136678090  

I believe that we should iterate until  Clock::now() >= t

> Username: austin-beer  
> Created_at: 2017-09-02 00:46:54 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685067  

See my comments in detail/thread.hpp.


---

## Review 24 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:53:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60256628  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 328 |-         cv_status
 323 |+         template <class lock_type, class Duration, class Predicate>
 324 |+         bool
```

> Username: viboes  
> Created_at: 2017-09-01 22:53:42 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136678204  

Why bool and not cv_status?

> Username: austin-beer  
> Created_at: 2017-09-01 23:03:43 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136679034  

Oops! Good catch. This is a copy/paste error.

> Username: austin-beer  
> Created_at: 2017-09-01 23:11:27 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136679623  

Actually, no, this isn't a copy-paste error. In the existing code the functions that take a predicate return a bool instead of cv_status. I stayed consistent with that behavior.


---

## Review 25 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:54:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60256688  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 356 |-             chrono::time_point<chrono::steady_clock, chrono::nanoseconds> tp)
 326 |+                 lock_type& lock,
 327 |+                 const chrono::time_point<internal_clock_t, Duration>& t,
```

> Username: viboes  
> Created_at: 2017-09-01 22:54:16 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136678247  

Agreed


---

## Review 26 [Commented]

> Username: viboes  
> Created_at: 2017-09-01 22:55:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60256878  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 352 |         {
 391 |-           return wait_until(lock, chrono::steady_clock::now() + d, boost::move(pred));
 353 |+           return wait_until(lock, internal_clock_t::now() + d, boost::move(pred));
```

> Username: viboes  
> Created_at: 2017-09-01 22:55:54 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136678389  

Agreed


---

## Comment 27

> Username: viboes  
> Created_at: 2017-09-02 00:26:28 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326709413  

I've created the branch and I'm working on it:  
  
You are right that we will need an absolute_internal_timespec.  
  
I have defined:   
timespec_duration  
real_timespec_timepoint  
real_timespec_clock  
mono_timespec_timepoint  
mono_timespec_clock  
  
and   
  
internal_timespec_timepoint  
internal_timespec_clock  
  
This makes much simpler things.  
  
However we cannot convert between clocks until we  know the epoch, and  we don't know the epoch for steady_clock.  
  
I've also extracted internal_clock_t to thread/detail/internal_clock.hpp  and renamed  detail::monotonic_pthread_cond_init to pthread::cond_init and extracted to pthread/pthread_helpers.hpp.  
  
I've used those in thread, condition_variable, mutex and recursive_mutex.  
  
I'm running the regression tests and will be able to have a first version tomorrow morning.  
  
Things I must do:  
* Inhibit pthread functions that don't have monotonic timed interface when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is defined  
* Add a loop on functions doing something until a generic Clock::timepoint (some of them are buggy)  
* Check each one of your proposed changes, in case I missed a bug in Boost.Thread code.   
* Simplify the code (later on)  
  
Thanks for raising those issues.

---

## Comment 28

> Username: austin-beer  
> Created_at: 2017-09-02 01:00:39 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326711656  

That all sounds good.  
  
The only issue I can think of is that you may break a lot of user's code if you inhibit pthread functions that don't take a monotonic timed interface when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled. I think it would be more useful if we kept those interfaces intact and just converted from system_clock to steady_clock. This doesn't break user's code but still gives them the benefit of a monotonic clock.  
  
The following conversion function doesn't provide a 100% reliable conversion from a system_clock time to a steady_clock time, but I think it is better than either inhibiting system_clock functions or not using the monotonic clock even when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled.  
  
steady_clock_time = ( system_clock_time - system_clock::now() ) + steady_clock::now()  
  
Just something to consider...

---

## Review 29 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 01:00:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60264768  

📁 include/boost/thread/detail/thread.hpp

```diff
 159 | #ifdef BOOST_THREAD_USES_CHRONO
 160 |- #if defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC)
 160 |+ #if defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC) || defined(BOOST_THREAD_PLATFORM_WIN32)
```

> Username: viboes  
> Created_at: 2017-09-02 01:00:50 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685491  

Why do you think that WIN32 has a monotonic clock?

> Username: austin-beer  
> Created_at: 2017-09-02 01:03:34 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136685577  

https://github.com/boostorg/chrono/blob/develop/include/boost/chrono/config.hpp#L68

> Username: viboes  
> Created_at: 2017-09-02 07:36:56 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691291  

Yes, it has a steady clock, but the WIN32 thread API Boost.Thread is using is a `system_clock` IIUC. I don't know Windows enough. Maybe there is an API that waits on a monotonic clock.   
  
So on windows, the `internal_clock_t` should be `system_clock` if I'm not wrong.

> Username: viboes  
> Created_at: 2017-09-02 07:40:41 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691345  

The same for MACOS. It has a steady_clock, but don't provide thread functions with MONOTONIC clocks, AFAIK.  
  
Please, let me know if I'm missing something important.

> Username: austin-beer  
> Created_at: 2017-09-02 17:53:57 UTC  
> Updated_at: 2017-09-02 17:53:58 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136700465  

I agree that MAC OS, even though it has a steady clock, doesn't support a steady clock with pthread_cond_init and so the internal clock for MAC OS needs to be system_clock.  
  
For Windows, however, it appears that all *wait_until() functions are internally converted to a duration. So using steady_clock on Windows as the internal clock should work just fine. And actually, because it converts everything to a duration, I don't think any Windows code should be using our internal clock typedefs or implementations at all. All Windows code should just be converting straight from a timepoint to a duration using the timepoint's now() function.

> Username: viboes  
> Created_at: 2017-09-03 21:11:50 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136726437  

You are completely right. I don't know why, I believed we where using the system time.  
  
There are some system_clock uses in win32/basic_timed_mutex.hpp  
```  
            template <class Rep, class Period>  
            bool try_lock_for(const chrono::duration<Rep, Period>& rel_time)  
            {  
              return try_lock_until(chrono::steady_clock::now() + rel_time);  
            }  
            template <class Clock, class Duration>  
            bool try_lock_until(const chrono::time_point<Clock, Duration>& t)  
            {  
              using namespace chrono;  
              system_clock::time_point     s_now = system_clock::now();  
              typename Clock::time_point  c_now = Clock::now();  
              return try_lock_until(s_now + ceil<system_clock::duration>(t - c_now));  
            }  
            template <class Duration>  
            bool try_lock_until(const chrono::time_point<chrono::system_clock, Duration>& t)  
            {  
              using namespace chrono;  
              typedef time_point<chrono::system_clock, chrono::system_clock::duration> sys_tmpt;  
              return try_lock_until(sys_tmpt(chrono::ceil<chrono::system_clock::duration>(t.time_since_epoch())));  
            }  
            bool try_lock_until(const chrono::time_point<chrono::system_clock, chrono::system_clock::duration>& tp)  
```  
  
My bad, surely :(  
  
However I see that it was already the case for the DAtTime interface  
  
```  
            bool timed_lock(::boost::system_time const& wait_until)  
            {  
                if(try_lock())  
                {  
                    return true;  
                }  
                long old_count=active_count;  
                mark_waiting_and_try_lock(old_count);  
  
                if(old_count&lock_flag_value)  
                {  
                    bool lock_acquired=false;  
                    void* const sem=get_event();  
  
                    do  
                    {  
                        if(win32::WaitForSingleObjectEx(sem,::boost::detail::get_milliseconds_until(wait_until),0)!=0)  
                        {  
                            BOOST_INTERLOCKED_DECREMENT(&active_count);  
                            return false;  
                        }  
                        clear_waiting_and_try_lock(old_count);  
                        lock_acquired=!(old_count&lock_flag_value);  
                    }  
                    while(!lock_acquired);  
                }  
                return true;  
            }  
  
            template<typename Duration>  
            bool timed_lock(Duration const& timeout)  
            {  
                return timed_lock(get_system_time()+timeout);  
            }  
```  
  
I'll try to see how to fix this.


---

## Comment 30

> Username: viboes  
> Created_at: 2017-09-02 07:31:12 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326728277  

> The only issue I can think of is that you may break a lot of user's code if you inhibit pthread functions that don't take a monotonic timed interface when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled. I think it would be more useful if we kept those interfaces intact and just converted from system_clock to steady_clock. This doesn't break user's code but still gives them the benefit of a monotonic clock.  
  
I meant to inhibit the use of pthreads functions   
  
```  
    #   if defined(BOOST_HAS_PTHREAD_DELAY_NP)  
    #     if defined(__IBMCPP__) ||  defined(_AIX)  
                  BOOST_VERIFY(!pthread_delay_np(const_cast<timespec*>(&ts.get())));  
    #     else  
                  BOOST_VERIFY(!pthread_delay_np(&ts.get()));  
    #     endif  
    #   elif defined(BOOST_HAS_NANOSLEEP)  
                  nanosleep(&ts.get(), 0);  
    #   else  
```  
  
> The following conversion function doesn't provide a 100% reliable conversion from a system_clock time to a steady_clock time, but I think it is better than either inhibiting system_clock functions or not using the monotonic clock even when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled.  
  
I don't see from where you deduce that I want to "inhibiting system_clock functions or not using the monotonic clock even when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is enabled"  
  
Sorry, the formula  
  
steady_clock_time = ( system_clock_time - system_clock::now() ) + steady_clock::now()  
  
is good, but I believe that we need to return cv_status::timeout only when `system_clock_time `is `>=`the new `system_clock::now()'.

---

## Review 31 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 07:47:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60270950  

📁 src/pthread/thread.cpp

```diff
 440 | 
 441 |-     #   if defined(BOOST_HAS_PTHREAD_DELAY_NP)
 441 |+     #   if defined(BOOST_HAS_PTHREAD_DELAY_NP) && !defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC)
```

> Username: viboes  
> Created_at: 2017-09-02 07:47:32 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691413  

Agreed


---

## Review 32 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 07:48:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60270958  

📁 src/pthread/thread.cpp

```diff
 450 |                   nanosleep(&ts, 0);
 451 |     #   else
 452 |+                   const timespec ts2 = boost::detail::timespec_plus_internal_clock(ts);
```

> Username: viboes  
> Created_at: 2017-09-02 07:48:03 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691420  

Agreed


---

## Review 33 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:13:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271253  

📁 include/boost/thread/pthread/thread_data.hpp

```diff
 294 |         {
 295 |-           return boost::this_thread::hidden::sleep_until_realtime(boost::detail::to_timespec(abs_time));
 295 |+           boost::this_thread::hidden::sleep_until_realtime(boost::detail::to_timespec(abs_time));
```

> Username: viboes  
> Created_at: 2017-09-02 08:13:02 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691654  

Agreed


---

## Review 34 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:13:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271255  

📁 include/boost/thread/pthread/thread_data.hpp

```diff
 300 |         {
 301 |-             this_thread::sleep(get_system_time()+rel_time);
 301 |+           boost::this_thread::hidden::sleep_for(boost::detail::to_timespec(rel_time));
```

> Username: viboes  
> Created_at: 2017-09-02 08:13:14 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691658  

agreed


---

## Review 35 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:18:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271308  

📁 include/boost/thread/pthread/recursive_mutex.hpp

```diff
 274 |         {
 249 |-             return timed_lock(get_system_time()+relative_time);
 275 |+             return do_try_lock_until(detail::timespec_plus_internal_clock(relative_time));
```

> Username: viboes  
> Created_at: 2017-09-02 08:18:19 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691708  

agreed. We need to use internal_clock here


---

## Review 36 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:22:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271382  

📁 include/boost/thread/pthread/recursive_mutex.hpp

```diff
 376 |         bool timed_lock(system_time const & abs_time)
 377 |         {
 352 |-             struct timespec const ts=detail::to_timespec(abs_time);
```

> Username: viboes  
> Created_at: 2017-09-02 08:22:35 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691754  

I will use here  
```  
real_timespec_timepoint const ts = abs_time;  
```


---

## Review 37 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:23:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271388  

📁 include/boost/thread/pthread/recursive_mutex.hpp

```diff
 384 |         {
 360 |-           return try_lock_until(chrono::steady_clock::now() + rel_time);
 385 |+           return try_lock_until(internal_clock_t::now() + rel_time);
```

> Username: viboes  
> Created_at: 2017-09-02 08:23:00 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691762  

agreed


---

## Review 38 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:23:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271393  

📁 include/boost/thread/pthread/recursive_mutex.hpp

```diff
 395 |         template <class Duration>
 371 |-         bool try_lock_until(const chrono::time_point<chrono::system_clock, Duration>& t)
 396 |+         bool try_lock_until(const chrono::time_point<internal_clock_t, Duration>& t)
```

> Username: viboes  
> Created_at: 2017-09-02 08:23:19 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691766  

agreed


---

## Review 39 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 08:25:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60271451  

📁 include/boost/thread/pthread/recursive_mutex.hpp

```diff
 397 |         {
 373 |-           using namespace chrono;
 374 |-           typedef time_point<system_clock, nanoseconds> nano_sys_tmpt;
```

> Username: viboes  
> Created_at: 2017-09-02 08:25:23 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136691828  

I will change here  
  
          typedef time_point<thread_detail::internal_clock_t, nanoseconds> nano_sys_tmpt;


---

## Comment 40

> Username: viboes  
> Created_at: 2017-09-02 12:47:05 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326742121  

Hi again.  
  
Here it is what I have done since yesterday https://github.com/boostorg/thread/tree/feature/timespec_clocks  
  
Tested on MAC-OS only.  
  
I'll continue replacing all the occurrences of `get_system_time()`.  
  
I've surely forgotten some additional cases.  
  
Once I've completed `get_system_time()` replacement, I would be interested in a test on linux with monotonic interface.  
  
Of course any test on Windows is already welcome.

---

## Review 41 [Commented]

> Username: viboes  
> Created_at: 2017-09-02 13:08:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/141#pullrequestreview-60275473  

📁 include/boost/thread/detail/thread.hpp

```diff
 539 |+           return do_try_join_until(boost::detail::to_timespec(tp.time_since_epoch()));
 540 |         }
 541 | #endif
```

> Username: viboes  
> Created_at: 2017-09-02 13:08:38 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136695609  

Here it is the endif of defined(BOOST_THREAD_PLATFORM_WIN32). So the next use of timespec is not good.  
This code is common.

> Username: austin-beer  
> Created_at: 2017-09-02 15:36:42 UTC  
> Updated_at: 2017-09-02 16:09:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#discussion_r136698028  

Yes, agreed. This change should be reverted, as it will break on Windows. My bad.


---

## Comment 42

> Username: austin-beer  
> Created_at: 2017-09-02 15:42:18 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326751753  

Ah, I see. I misunderstood what you meant when you said "inhibit pthreads functions". Yes, I agree with doing that, and I think I already did that myself in v2/thread.hpp.

---

## Comment 43

> Username: austin-beer  
> Created_at: 2017-09-02 15:43:25 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326751834  

I'd be happy to test your changes on Linux with a monotonic clock once you're ready for me to. Linux is my primary development platform. I'm not nearly as familiar with Windows either. :-(

---

## Comment 44

> Username: austin-beer  
> Created_at: 2017-09-02 16:43:50 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326755492  

> I believe that we need to return cv_status::timeout only when system_clock_time is >=the new `system_clock::now()'.  
  
I still think we should return cv_status::timeout only when do_wait_until() times out. Here's the scenario I'm trying to avoid:  
  
- BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is defined, so do_wait_until() is using the steady clock.  
- Some other code calls wait_until() in a loop until wait_until() returns cv_status::timeout, and it passes it a system time.  
- wait_until() converts the system time to a steady time and calls do_wait_until().  
- do_wait_until() times out after waiting the correct amount of time. But, while it was waiting, the system clock jumped.  
- If wait_until() compares against the system clock to determine whether or not to return cv_status::timeout, it may incorrectly report that it did _not_ time out, even though do_wait_until() did time out, which will cause the other code's while loop to incorrectly call wait_until() again.  
  
Does that make sense?

---

## Comment 45

> Username: viboes  
> Created_at: 2017-09-03 14:22:36 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326807986  

@brainwave64   
I know it is surely not enoug https://github.com/boostorg/thread/commit/a6d5b5e576149838f4b14908f4b0c1a41fd01636, but please,could you give a try to this branch.

---

## Comment 46

> Username: viboes  
> Created_at: 2017-09-03 14:25:17 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326808153  

Concerning the `cv_status`, returning `cv_status::timeout` when the type has not been reached seems to me an error. This is why I believe we should iterate again, until either `cv_status::no_timeout` or `t <=now()`.  
  
I've yet to fix in my branch.

---

## Comment 47

> Username: austin-beer  
> Created_at: 2017-09-03 21:46:03 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326833241  

> Concerning the cv_status, returning cv_status::timeout when the type has not been reached seems to me an error. This is why I believe we should iterate again, until either cv_status::no_timeout or t <=now().  
  
Let me see if I can clarify where we disagree. Suppose a user gets the current system time, adds one second to it, and then passes that new timepoint to `wait_until()`. While `wait_until()` is waiting, the system time jumps backwards one second. There are three possible ways `wait_until()` could behave in this situation.  
  
1. Wait for two seconds and then return `cv_status::timeout`. This is the current behavior with no monotonic clock.  
2. Wait for one second and then return `cv_status::timeout`. This is the behavior of my PR code when the monotonic clock is enabled.  
3. Wait for one second and then return `cv_status::no_timeout`. This is the behavior when the monotonic clock is enabled and we compare `t` against `Clock::now()` to determine which `cv_status` to return.  
  
I think `#2` is better than `#3` because I think that 99% of the time, when a user passes us a system time that's in the future, they are expecting to wait for a fixed duration of time from now, and for the `cv_status` to indicate whether or not that fixed duration of time was reached.  
  
However, I will concede that `#3` can be considered technically correct because the user passed us a system timepoint, not a steady timepoint, and so we should assume that they did so knowing the system time might jump. So I could go either way. I just think that `#2` will result in less unexpected behavior for users when the monotonic clock is enabled.

---

## Comment 48

> Username: austin-beer  
> Created_at: 2017-09-03 21:55:24 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326833720  

I've tested out your branch and it looks pretty good so far. I had to make a few corrections to the code in order to get it to compile. I've included those in the diff below.  
  
[compile_fixes.diff.txt](https://github.com/boostorg/thread/files/1273282/compile_fixes.diff.txt)  
  
I've also included the results of my testing. The only tests that failed were the ones for functions that currently implement the `#3` behavior instead of the `#2` behavior (see my previous comment), since my tests are designed assuming `#2` behavior. Other than that everything now behaves much better with the monotonic clock enabled than it did in v1.65.  
  
[results.txt](https://github.com/boostorg/thread/files/1273281/results.txt)

---

## Comment 49

> Username: viboes  
> Created_at: 2017-09-03 22:46:05 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326836255  

I believe we should have `#1`. If the user is "expecting to wait for a fixed duration of time from now", it should use a duration. I believe this is independent of whether we have monotonic platform interface. See  http://talesofcpp.fusionfenix.com/post-15/rant-on-the-templated-nature-of-stdchrono, where Agustin shows that even with non-monotonic interface we can manage with clock adjustments by polling.  
  
`#2` doesn't respect the contract.  
`#3`is not valid, when the result of the first wait is successful. This is why I believe that we should iterate until success or the time-point is reached.   
  
If the user wants to #2, it can use a duration instead of a system time_point.

---

## Comment 50

> Username: austin-beer  
> Created_at: 2017-09-03 23:06:29 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326837517  

Ok, you've convinced me. We should be doing `#1`, even when the underlying implementation is using a monotonic clock. That article was very helpful. Thank you for pointing me to it.  
  
I'll update my test code to expect `#1` behavior when passed a system clock timepoint.  
  
`#1` is a bit challenging to implement well when the monotonic clock is enabled, specifically in the case where the system clock jumps forward. I believe Agustin suggested implementing a maximum threshold in the polling loop so that `wait_until()` returns as soon as possible when the system clock jumps forward. Are you considering adding a threshold as well?

---

## Comment 51

> Username: viboes  
> Created_at: 2017-09-03 23:14:42 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326837908  

Thanks for the test and the patch. Sorry for the code that contained compile errors, I was unable to compile it.  
  
I don't understand why you need `real_timespec_timepoint` in  
  
```  
diff --git a/include/boost/thread/pthread/thread_data.hpp b/include/boost/thread/pthread/thread_data.hpp  
index 275a6634..f8f3137e 100644  
--- a/include/boost/thread/pthread/thread_data.hpp  
+++ b/include/boost/thread/pthread/thread_data.hpp  
@@ -244,7 +244,7 @@ namespace boost  
         namespace hidden  
         {  
           void BOOST_THREAD_DECL sleep_for(const detail::timespec_duration& ts);  
-          void BOOST_THREAD_DECL sleep_until_realtime(const detail::internal_timespec_timepoint& ts);  
+          void BOOST_THREAD_DECL sleep_until_realtime(const detail::real_timespec_timepoint& ts);  
         }  
   
 #ifdef BOOST_THREAD_USES_CHRONO  
@@ -265,7 +265,7 @@ namespace boost  
           namespace hidden  
           {  
             void BOOST_THREAD_DECL sleep_for(const detail::timespec_duration& ts);  
-            void BOOST_THREAD_DECL sleep_until_realtime(const detail::internal_timespec_timepoint& ts);  
+            void BOOST_THREAD_DECL sleep_until_realtime(const detail::real_timespec_timepoint& ts);  
           }  
   
     #ifdef BOOST_THREAD_USES_CHRONO  
@@ -292,13 +292,13 @@ namespace boost  
 #endif  
         inline void sleep(system_time const& abs_time)  
         {  
-          boost::this_thread::hidden::sleep_until_realtime(detail::internal_timespec_timepoint(abs_time));  
+          boost::this_thread::hidden::sleep_until_realtime(detail::real_timespec_timepoint(abs_time));  
         }  
   
         template<typename TimeDuration>  
         inline BOOST_SYMBOL_VISIBLE void sleep(TimeDuration const& rel_time)  
         {  
-          boost::this_thread::hidden::sleep_until_realtime(detail::internal_timespec_clock::now() + detail::timespec_duration(rel_time));  
+          boost::this_thread::hidden::sleep_until_realtime(detail::real_timespec_clock::now() + detail::timespec_duration(rel_time));  
         }  
 #endif // BOOST_THREAD_USES_DATETIME  
     } // this_thread  
```  
  
I understand that I need to rename `sleep_until_realtime` to `sleep_until`. This was on my todo list, but I forget to do it.  
  
I'm wondering if   
  
```  
#if defined BOOST_THREAD_USES_DATETIME  
      inline mono_timespec_timepoint(boost::system_time const& abs_time);  
#endif  
#if defined BOOST_THREAD_USES_CHRONO  
      inline mono_timespec_timepoint(chrono::time_point<chrono::system_clock, chrono::nanoseconds> const& abs_time);  
#endif  
```  
  
and should define only implicit constructions  from steady clocks  
```  
      inline mono_timespec_timepoint(chrono::time_point<chrono::steady_clock, chrono::nanoseconds> const& abs_time);  
```

---

## Comment 52

> Username: austin-beer  
> Created_at: 2017-09-03 23:19:18 UTC  
> Updated_at: 2017-09-03 23:20:25 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326838097  

Changing sleep_until_realtime was necessary in order to get it to compile, because in src/pthread/thread.cpp sleep_until_realtime is defined to take real_timespec_timepoint. But I think I should have changed src/pthread/thread.cpp instead of thread_data.hpp to take internal_timespec_timepoint.

---

## Comment 53

> Username: austin-beer  
> Created_at: 2017-09-03 23:30:17 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326838603  

This is just a thought. I don't know if it's a good idea or not.  
  
What if condition_variable, condition_variable_any, mutex, and recursive_mutex each contained two pthread_cond_t variables internally instead of one? One would be initialized with the steady clock, and one would be initialized with the system clock. All pthread_cond_signal() and pthread_cond_broadcast() calls would be sent to both condition variables. If the user provides us with a system timepoint, we would wait on the system-clock-initialized condition variable. If the user provides us with a duration or with a steady timepoint, we would wait on the steady-clock-initialized condition variable.  
  
This would introduce additional runtime overhead into the implementation, so it may not be worth it. But it would eliminate the need for us to poll on the condition variable and thus would provide the most reliable behavior when the user provides a system timepoint and the system clock jumps backwards or forwards. And it would eliminate the need to convert from system timepoints to mono_timespec_timepoint.

---

## Comment 54

> Username: viboes  
> Created_at: 2017-09-03 23:41:40 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326839733  

There are 3 way to implement the time-related functions depending on the interface of the platform  
  
system time-point - MacOs  
  
    - steady implementation  
  
        - We should iterate by polling with a time-point that is  e.g. 1 sec after real_now  
  
    - system implementation  
  
        - We could use the system time directly and we risk to wait more than needed or  
  
        - We should iterate by polling with a time-point that is  e.g. 1 sec after real_now  
  
steady time-point - Linux  
  
    - system implementation  
  
        - we should iterate until success or the time reached using the mono_now + t - real_now and we risk to wait more than needed  or  
  
        - We should iterate by polling with a time-point that is  e.g. 1 sec after mono_now  
  
    - steady  
  
        - We could use the steady time directly as there is no risk  
  
  
duration - Windows  
    - system implementation  
  
        - we should iterate until success or the time reached using the mono_now + t - real_now and we risk to wait more than needed  or  
  
        - We should iterate by polling with a time-point that is  e.g. 1 sec after mono_now  
  
    - steady  
  
        - We could use the steady duration directly as there is no risk

---

## Comment 55

> Username: viboes  
> Created_at: 2017-09-03 23:44:26 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326839881  

I don't think having two condition variables is a good thing, but let me thing a little bit more on it.  
  
waiting on a system time-point that can be adjusted is something would need to pool if we don't want to wait too long time.

---

## Comment 56

> Username: viboes  
> Created_at: 2017-09-03 23:45:14 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326839919  

I'll try to implement `#1` during the week.

---

## Comment 57

> Username: viboes  
> Created_at: 2017-09-03 23:50:26 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326840189  

I've updated the branch with some of your corrections.  
  
  
I've not added yet the construction of mono_timespec_timepoint from real_timespec_timepoint.  
  
Please, let me know of the compiler errors.

---

## Comment 58

> Username: austin-beer  
> Created_at: 2017-09-03 23:50:32 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326840196  

From my testing with a recent Linux kernel, when the system time jumps forwards while we're waiting on a system-clock initialized condition variable, the condition variable returns early. Likewise, when the system clock jumps backwards, it waits the additional amount of time before returning. In both cases, `#1` behavior occurs without a risk of waiting longer than needed.

---

## Comment 59

> Username: austin-beer  
> Created_at: 2017-09-03 23:50:51 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326840218  

Ok, I'll pull in your changes and re-compile.

---

## Comment 60

> Username: austin-beer  
> Created_at: 2017-09-03 23:56:10 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326840501  

sleep_until_realtime needs to be fixed in src/pthread/thread.cpp.

---

## Comment 61

> Username: austin-beer  
> Created_at: 2017-09-03 23:58:32 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326840618  

do_try_join_until_noexcept also needs to be fixed in src/pthread/thread.cpp. Essentially just replace all instances of `real_` with `internal_` in that file and it will compile on Linux.

---

## Comment 62

> Username: viboes  
> Created_at: 2017-09-04 05:36:15 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326869984  

Sorry, I missed this file on the commit :(

---

## Comment 63

> Username: viboes  
> Created_at: 2017-09-04 06:16:59 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326875006  

Wondering if there is not an cycle / pool issue here  
  
```  
#if defined BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC && defined BOOST_CHRONO_HAS_CLOCK_STEADY  
    template <class Rep, class Period>  
    void sleep_for(const chrono::duration<Rep, Period>& d)  
    {  
      using namespace chrono;  
      if (d > duration<Rep, Period>::zero())  
      {  
        steady_clock::time_point c_timeout = steady_clock::now() + ceil<nanoseconds>(d);  
        sleep_until(c_timeout);  
      }  
    }  
  
    template <class Duration>  
    inline BOOST_SYMBOL_VISIBLE  
    void sleep_until(const chrono::time_point<chrono::steady_clock, Duration>& t)  
    {  
      using namespace chrono;  
      sleep_for(t - steady_clock::now());  
    }  
```  
  
While this finish by working, it is pooling. IMO, the last one should be removed.

---

## Comment 64

> Username: austin-beer  
> Created_at: 2017-09-04 08:31:18 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326900090  

I think both of these functions should be removed, and  
  
`#elif defined BOOST_THREAD_SLEEP_FOR_IS_STEADY`  
  
should be replaced by  
  
`#if defined(BOOST_THREAD_SLEEP_FOR_IS_STEADY) && !defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC)`  
  
That's what I did in my PR.

---

## Comment 65

> Username: austin-beer  
> Created_at: 2017-09-04 08:31:46 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-326900207  

And thanks for committing that file. It compiles without issue now!

---

## Comment 66

> Username: austin-beer  
> Created_at: 2017-09-04 17:10:40 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327003763  

I've updated my tests to expect `#1` behavior and re-run the tests against the latest code in your branch. Hopefully this will help you see which functions need attention.  
  
I've also gotten my tests running on Windows under Visual Studio 2015. I've included those results as well. I only had to make a couple of changes, also included below, to get those tests to compile and run.  
  
[compile_fixes.diff.txt](https://github.com/boostorg/thread/files/1275243/compile_fixes.diff.txt)  
[linux_results.txt](https://github.com/boostorg/thread/files/1275245/linux_results.txt)  
[windows_results.txt](https://github.com/boostorg/thread/files/1275246/windows_results.txt)

---

## Comment 67

> Username: viboes  
> Created_at: 2017-09-04 20:21:17 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327023844  

Please, could you upload the test file with the changes?  
How do you calculate the expected time?  
How much time the clock is back or forward?  
  
Thanks for all

---

## Comment 68

> Username: austin-beer  
> Created_at: 2017-09-05 03:30:07 UTC  
> Updated_at: 2017-09-05 03:34:49 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327062503  

Here is the updated test file. I also included the Makefiles I'm using in case those are helpful to you. Just be aware some of the paths are hard-coded for my environment.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1275962/test_boost_sleep.cpp.txt)  
[Makefile.linux.txt](https://github.com/boostorg/thread/files/1275964/Makefile.linux.txt)  
[Makefile.win32.txt](https://github.com/boostorg/thread/files/1275965/Makefile.win32.txt)  
  
For these tests, I get the current system or steady clock time (depending on the test), add 500 milliseconds, and then pass the result to the function being tested. Then, in a separate thread 100 milliseconds after calling the function, I change the system clock either forward or backward by 300 milliseconds.  
  
For functions using the steady clock, they should wait 500 milliseconds regardless of how the system clock changed. For functions using the system clock, they should wait only 200 milliseconds when the system clock jumps forward by 300 milliseconds, and they should wait 800 milliseconds when the system clock jumps backward by 300 milliseconds. These are the expected values I check for.

---

## Comment 69

> Username: viboes  
> Created_at: 2017-09-05 06:00:04 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327079004  

Thanks for the clarifications.  
  
Glad to see that you could check on Windows as well.  
  
Could you check, setting the internal_clock_t to steady_clock on windows?  
```  
  #if defined(BOOST_THREAD_PLATFORM_WIN32)  
          typedef chrono::steady_clock internal_clock_t;  
```

---

## Comment 70

> Username: viboes  
> Created_at: 2017-09-05 10:51:41 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327141024  

I've committed all your fixes and started to iterate while waiting until condition variables.  
Please, let me know what are the results. Are there some improvements? Regressions?

---

## Comment 71

> Username: austin-beer  
> Created_at: 2017-09-05 16:32:20 UTC  
> Updated_at: 2017-09-05 16:39:30 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327231474  

Changing the internal clock on Windows didn't change any of the test results. I did have to make one change to get it to compile, though. In `include/boost/thread/detail/thread.hpp`:  
`typename Clock::duration   d = ceil<nanoseconds>(t-Clock::now());`  
had to be changed to:  
`typename Clock::duration   d = ceil<typename Clock::duration>(t-Clock::now());`  
because the system clock on Windows uses a duration that is in units of 100-nanoseconds.  
  
On Linux, your changes caused two additional tests to succeed that didn't before. There were no regressions:  
  
* Testing boost::condition_variable::wait_until() with a system clock time:  
    * While system clock jumps back: Succeeded  
* Testing boost::condition_variable_any::wait_until() with a system clock time:  
    * While system clock jumps back: Succeeded  
  
These two tests still fail when the system clock jumps forward (it still waits too long), but that's to be expected since your changes didn't address that.

---

## Comment 72

> Username: viboes  
> Created_at: 2017-09-05 16:44:41 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327234897  

Great. I 'll take in account the windows fix. Maybe we need something more specific, as the internal duration is nanoseconds on posix and milliseconds on windows, if I'm not wrong.  
  
Concerning the yet failing jump forward tests:  
* How much long?  
* How would you address the jump forward?

---

## Comment 73

> Username: austin-beer  
> Created_at: 2017-09-05 16:50:13 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327236534  

When the system clock jumps forward by 300 milliseconds, the wait_until() function still waits for 500 milliseconds. It should only wait for 200 milliseconds. So it waits 300 milliseconds too long.  
  
There are only two solutions I can think of right now, but I'll think about it some more and see if I can come up with anything better.  
* Add a maximum threshold (e.g. 100 milliseconds) on how long we wait so that we poll Clock::now() more frequently.  
* Add a second internal pthread_cond_t variable that's initialized to use the system clock instead of the steady clock, and wait on that condition variable instead.

---

## Comment 74

> Username: viboes  
> Created_at: 2017-09-05 17:37:03 UTC  
> Updated_at: 2017-09-05 17:47:10 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327249790  

Ok. I see. So when the system time is changed and we are waiting on a monotonic condition, there is no timeout or something else. Jumping backward is not a problem as we need to wait even more time, than requested on the monotonic clock.  
  
I understand now why you susggested a second pthread_condition_t variable.  
  
Polling every 100 milliseconds could be quite expensive.   
  
A third possibility could be to use timers. Instead of waiting for some time on a condition variable we could just arm a timer and wait on the condition (without time). When the timer timeouts it will notify the condition variable. The major problem is complexity and that this needs additional memory for the timer.   
  
I believe that we could implement the polling first and see how it works.  
Then we could implement the 2 pthread_cond_t variables.   
  
After all, maybe the solution with a system condition variable wouldn't be so bad, if it is ensured that the wait on a  pthread_cond_t will finish when the system time is changed. Do you have any pointer where this feature is documented (required)?

---

## Comment 75

> Username: viboes  
> Created_at: 2017-09-05 17:46:09 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327252325  

Could you share the results, when  BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is not defined?

---

## Comment 76

> Username: viboes  
> Created_at: 2017-09-05 17:54:24 UTC  
> Updated_at: 2017-09-05 17:54:36 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327254603  

More on the timer solution. It is of course much more complex than I believed originally. We need cancellation and know that the system time has been changed :(   
  
All this should be provided by pthread_cond_t on system clock if wait finish when the system time changes.

---

## Comment 77

> Username: austin-beer  
> Created_at: 2017-09-05 17:59:35 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327255872  

I don't have any documentation that specifies how pthread_cond_t should behave when the system time jumps forward. All I have is my testing on Linux that indicates that it will return early like we want it to.

---

## Comment 78

> Username: austin-beer  
> Created_at: 2017-09-05 19:08:27 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327273331  

This is an interesting read. It looks like Mike Crowe started down the road of updating glibc so that a single pthread_cond_t variable could be used with multiple clocks via a new pthread_cond_timedwaitonclock_np function. That's essentially the same method as having two pthread_cond_t variables, only more efficient.  
  
https://gcc.gnu.org/bugzilla/show_bug.cgi?id=41861

---

## Comment 79

> Username: austin-beer  
> Created_at: 2017-09-05 19:15:54 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327275153  

I updated my tests again to test the sleep functions both when they are called from within a boost thread and when they are called from outside of a boost thread. When they are called from outside of a boost thread, they use the no_interruption_point implementations in `src/pthread/thread.cpp`, which weren't being tested before.  
  
Here are the latest results run against the current code in feature/timespec_clocks. As would be expected, enabling BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC has no effect on the Windows results.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1278597/test_boost_sleep.cpp.txt)  
[linux_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1278596/linux_results_no_monotonic.txt)  
[linux_results_with_monotonic.txt](https://github.com/boostorg/thread/files/1278598/linux_results_with_monotonic.txt)  
[windows_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1278599/windows_results_no_monotonic.txt)  
[windows_results_with_monotonic.txt](https://github.com/boostorg/thread/files/1278600/windows_results_with_monotonic.txt)

---

## Comment 80

> Username: viboes  
> Created_at: 2017-09-05 19:33:28 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327279656  

Thanks for reporting. Yes, windows is independent of `BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC`  
  
These results are weird  
```  
boost::condition_variable::wait_for():  
    While system clock remains stable: Succeeded  
    While system clock jumps back:     FAILED: Expected a wait of 500 ms but actual wait was 801 ms (too high)  
    While system clock jumps forward:  FAILED: Expected a wait of 500 ms but actual wait was 200 ms (too low)  
boost::condition_variable::wait_until(), system clock time:  
    While system clock remains stable: Succeeded  
    While system clock jumps back:     Succeeded  
    While system clock jumps forward:  Succeeded  
```  
  
`wait_for(d)` is defined as `wait_until(system_clock::now()+d)`.  
  
If `wait_until` succeeds while jumping, why `wait_for` doesn't? Do you have an explanation?

---

## Comment 81

> Username: austin-beer  
> Created_at: 2017-09-05 19:41:06 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327281542  

The test for wait_until() with a system clock time expects wait_until() to wait 800 milliseconds when the clock jump backwards and 200 milliseconds when the clock jumps forward. When BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is disabled, that's exactly what it does, so the test succeeds.  
  
But wait_for() is expected to wait 500 milliseconds regardless of whether or not the system clock jumps, which is does not. Thus those tests fail.

---

## Comment 82

> Username: viboes  
> Created_at: 2017-09-05 20:14:07 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327289992  

Thanks for the bugzilla  link. Very instructive.  
  
BTW, it would be interesting to know if the wait until/for returned no_timeout/timeout.  
IIUC, it is correct to return timeout only if the time was reached or the duration elapsed.  
So for the tests that fail with (too low) the result should be no_timeout, and for the test that fail with (too high) or succeed the result should be timeout.  
  
Any failure at the interface level will be an error, while waiting more or less will be a QOI. I'm not saying that I don't want to improve the QOI (the probe is we are working on this), but before addressing the QOI, I would like to be sure we have a conforming implementation.  
  
Please, could you add this information on the report?   
  
Thanks in advance for all your help.

---

## Comment 83

> Username: viboes  
> Created_at: 2017-09-05 20:30:01 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327293970  

Ok, understood for the difference between wait_until and wait_for.

---

## Comment 84

> Username: austin-beer  
> Created_at: 2017-09-05 21:28:22 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327308745  

Another compile error I just found in `include/boost/thread/v2/thread.hpp`  
Change:  
`ceil<thread_detail::internal_clock_t::::duration>(d)`  
To:  
`ceil<thread_detail::internal_clock_t::duration>(d)`

---

## Comment 85

> Username: viboes  
> Created_at: 2017-09-05 21:32:58 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327309858  

Concerning   
```  
 typename Clock::duration d = ceil<typename Clock::duration>(t-Clock::now());  
```  
  
I believe that it should be   
```  
 typename Clock::duration d = t-Clock::now();  
```  
  
as the ceil is done already on wait_until, isn't it?

---

## Comment 86

> Username: austin-beer  
> Created_at: 2017-09-05 21:36:57 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327310790  

Here are the updated test results you requested. I also added tests for the no_interruption_point versions of the sleep functions.  
  
Currently only two tests on Windows return `no_timeout` when they fail with "too short". All of the other tests that fail with "too short" return `timeout`.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1278983/test_boost_sleep.cpp.txt)  
[linux_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1278984/linux_results_no_monotonic.txt)  
[linux_results_with_monotonic.txt](https://github.com/boostorg/thread/files/1278985/linux_results_with_monotonic.txt)  
[windows_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1278986/windows_results_no_monotonic.txt)

---

## Comment 87

> Username: austin-beer  
> Created_at: 2017-09-05 21:42:12 UTC  
> Updated_at: 2017-09-05 21:42:29 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327311960  

> Concerning  
>   
>  typename Clock::duration d = ceil<typename Clock::duration>(t-Clock::now());  
> I believe that it should be  
>   
>  typename Clock::duration d = t-Clock::now();  
> as the ceil is done already on wait_until, isn't it?  
  
Yes, you are correct. I tested your change and it compiles without issue.

---

## Comment 88

> Username: viboes  
> Created_at: 2017-09-05 22:09:14 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327317848  

I have committed changes with the compile fixes you have reported and some that should fix the condition variable functions with chrono interface for linux-monotonic and linux-no-monotonic configurations.

---

## Comment 89

> Username: austin-beer  
> Created_at: 2017-09-05 22:24:51 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327320908  

Here are the test results with your changes. The results don't indicate any regressions, and more of the tests pass now. I am going to update my test to not use times that are a multiple of 100 milliseconds, since that's the value you now use in your polling loop. I think we may be getting some false Passes as a result, but we'll see.  
  
[linux_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1279130/linux_results_no_monotonic.txt)  
[linux_results_with_monotonic.txt](https://github.com/boostorg/thread/files/1279131/linux_results_with_monotonic.txt)

---

## Comment 90

> Username: austin-beer  
> Created_at: 2017-09-05 23:06:20 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327327977  

Changing the wait and jump times in my tests didn't change the results, so I think we're on the right track with the polling loop code. I looked at what you did and it looks really good to me. Two pthread_cond_t variables would provide somewhat better QOI, but with the cost of additional overhead, and it would still require polling for user-defined clocks (should anyone ever do that). So I think the polling solution with a 100 millisecond max is a good compromise.  
  
The 100 milliseconds could be made configurable by the user if we're worried about performance.  
  
One small thing to perhaps account for. We should only use chrono::steady_clock when BOOST_CHRONO_HAS_CLOCK_STEADY is defined. As such, we should not allow BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC to be defined if BOOST_THREAD_USES_CHRONO is defined but BOOST_CHRONO_HAS_CLOCK_STEADY is not defined.

---

## Comment 91

> Username: viboes  
> Created_at: 2017-09-06 06:30:15 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327388223  

Yes, the 100 milliseconds should be configurable at compile time.  
  
I was wondering if the polling period couldn't be an additional parameter of the time related functions, defaulted to the configured one, or have an additional overload. In this way the user is always able master the granularity of the possible polling.

---

## Comment 92

> Username: viboes  
> Created_at: 2017-09-06 06:30:21 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327388238  

Concerning  BOOST_CHRONO_HAS_CLOCK_STEADY, yes, you are right, but currently Boost.Chrono defines always BOOST_CHRONO_HAS_CLOCK_STEADY If I'm not wrong.

---

## Comment 93

> Username: viboes  
> Created_at: 2017-09-06 10:58:03 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327449067  

Committed "try a fix for sleep_for/sleep_until on linux."

---

## Comment 94

> Username: austin-beer  
> Created_at: 2017-09-06 15:55:38 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327529551  

> Yes, the 100 milliseconds should be configurable at compile time.  
>   
> I was wondering if the polling period couldn't be an additional parameter of the time related functions, defaulted to the configured one, or have an additional overload. In this way the user is always able master the granularity of the possible polling.  
  
That's a good idea. I like it.

---

## Comment 95

> Username: austin-beer  
> Created_at: 2017-09-06 15:59:52 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327530845  

> Concerning BOOST_CHRONO_HAS_CLOCK_STEADY, yes, you are right, but currently Boost.Chrono defines always BOOST_CHRONO_HAS_CLOCK_STEADY If I'm not wrong.  
  
https://github.com/boostorg/chrono/blob/develop/include/boost/chrono/config.hpp#L85  
  
The only possibility of BOOST_CHRONO_HAS_CLOCK_STEADY not being defined is if Boost is being compiled on a POSIX OS that doesn't support CLOCK_MONOTONIC. But I don't know if any such OSes exist anymore, so maybe it's safe to assume that CLOCK_MONOTONIC will always be present.

---

## Comment 96

> Username: viboes  
> Created_at: 2017-09-06 16:44:05 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327543673  

Hrr, this means that we need to protect any use of steady_clock. :(   
And change the algorithm when not present.  
The code on develop/release branches make use of steady_clock without protection and no one has reported any issue.   
I believe that I'll not protect it for the time being.

---

## Comment 97

> Username: austin-beer  
> Created_at: 2017-09-06 16:48:22 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327544988  

> The code on develop/release branches make use of steady_clock without protection and no one has reported any issue.  
> I believe that I'll not protect it for the time being.  
  
Sounds good to me.

---

## Comment 98

> Username: viboes  
> Created_at: 2017-09-06 17:00:53 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327548620  

I missed you comment about timeout-no_timeout. Thank for the change and report.  
And glad to see that almost all the functions are reporting timeout :)  
I'll address the Windows issues after the linux ones.

---

## Comment 99

> Username: austin-beer  
> Created_at: 2017-09-06 17:24:15 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327554871  

Unfortunately, your sleep changes didn't fix any of the test results because of the presence of BOOST_THREAD_SLEEP_FOR_IS_STEADY and the fact that the `sleep*()` functions in `src/pthread/thread.cpp` get called when that is enabled. However, once I removed the BOOST_THREAD_SLEEP_FOR_IS_STEADY code, your changes caused additional tests to pass. See the file below for the changes I made and the subsequent test results.  
  
I did have to fix an issue in the while loops. I added `d = t - Clock::now();` to them.  
  
I also added a custom clock to my tests that is based on the system clock but with a different epoch. I just want to make sure that we don't break other clocks with these changes.  
  
[fixes.diff.txt](https://github.com/boostorg/thread/files/1282015/fixes.diff.txt)  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1282014/test_boost_sleep.cpp.txt)  
[linux_results_no_monotonic.txt](https://github.com/boostorg/thread/files/1282016/linux_results_no_monotonic.txt)  
[linux_results_with_monotonic.txt](https://github.com/boostorg/thread/files/1282017/linux_results_with_monotonic.txt)

---

## Comment 100

> Username: viboes  
> Created_at: 2017-09-06 21:25:11 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327616827  

Sorry for the missing `d = t - Clock::now();`.   
Maybe there are some additional issues, but I want to preserve `BOOST_THREAD_SLEEP_FOR_IS_STEADY` for platforms that have `BOOST_THREAD_SLEEP_FOR_IS_STEADY`  but `BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC`.  
  
I recognize that there are some code to rework on thread_data. I'd need to check it.

---

## Comment 101

> Username: viboes  
> Created_at: 2017-09-06 21:25:51 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327616968  

The custom clocks is an excellent idea.

---

## Comment 102

> Username: viboes  
> Created_at: 2017-09-06 21:41:21 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327620275  

After inspection. I have not see major anything to change in thread_data.hpp.  
  
Please redo a check, and let me know if some of your changes are still needed.  
  
I've committed changes on mutex and  recursive_mutex.

---

## Comment 103

> Username: austin-beer  
> Created_at: 2017-09-07 03:43:48 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327675622  

I had to make just a couple of minor updates to your latest commit to get it to compile and run. The reason I changed `#if defined BOOST_THREAD_SLEEP_FOR_IS_STEADY` to `#if defined BOOST_THREAD_SLEEP_FOR_IS_STEADY && !defined BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC` is because, without it, when `BOOST_THREAD_SLEEP_FOR_IS_STEADY` was defined, there ended up being two `sleep_until(const chrono::time_point<chrono::steady_clock, Duration>& t)` functions because internal_clock_t is a typedef of chrono::steady_clock.  
  
[compile_fixes.diff.txt](https://github.com/boostorg/thread/files/1283375/compile_fixes.diff.txt)  
[linux_results_no_monotonic_1.txt](https://github.com/boostorg/thread/files/1283377/linux_results_no_monotonic_1.txt)  
[linux_results_with_monotonic_1.txt](https://github.com/boostorg/thread/files/1283376/linux_results_with_monotonic_1.txt)  
  
Unfortunately there were still some sleep tests that failed. I took the liberty of making some additional changes that kept the BOOST_THREAD_SLEEP_FOR_IS_STEADY functionality (which should be kept, as you correctly pointed out), but that simplified the rest of the sleep code and caused the rest of the sleep tests to pass.  
  
[sleep_fixes.diff.txt](https://github.com/boostorg/thread/files/1283387/sleep_fixes.diff.txt)  
[linux_results_no_monotonic_2.txt](https://github.com/boostorg/thread/files/1283386/linux_results_no_monotonic_2.txt)  
[linux_results_with_monotonic_2.txt](https://github.com/boostorg/thread/files/1283388/linux_results_with_monotonic_2.txt)  
  
The mutex and recursive_mutex changes look good.

---

## Comment 104

> Username: viboes  
> Created_at: 2017-09-07 11:11:02 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327768897  

The following lines in thread_data.hpp  
  
```  
 #ifdef BOOST_THREAD_USES_CHRONO  
-        template <class Rep, class Period>  
-        void sleep_for(const chrono::duration<Rep, Period>& d);  
```  
  
are there to prevent sleep_for to be instantiated for other duration than nanoseconds when this file is included, but v2/thread.hpp is not. Maybe I would need to add a comment.

---

## Comment 105

> Username: austin-beer  
> Created_at: 2017-09-07 14:11:42 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327811622  

Ah, that makes sense. Yes, a comment would be helpful, since that reason wasn't obvious to me, and probably wouldn't be to others either. Thanks.

---

## Comment 106

> Username: viboes  
> Created_at: 2017-09-07 20:43:02 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327922145  

I've not had time to analyze all your changes has needed.  
I've committed the ones that were evident.

---

## Comment 107

> Username: austin-beer  
> Created_at: 2017-09-08 00:27:26 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-327966122  

No problem. Just let me know once you've had a chance to. Thanks!

---

## Comment 108

> Username: viboes  
> Created_at: 2017-09-17 15:30:56 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-330056794  

Hi, I've re-started with this issue.  
  
Your patch has an issue, I believe.  
  
sleep_for/sleep_until are interruption points and so we must use a condition variable.  
  
I added no_interruption_point::sleep_for/sleep_until in order to provide sleep that are not interruption points.  
  
What do you think?

---

## Comment 109

> Username: viboes  
> Created_at: 2017-09-17 17:17:37 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-330063526  

I have committed more changes.  
  
https://github.com/boostorg/thread/commit/82eed2ca6d3b1573461ebcae19a9096eeff50799  
https://github.com/boostorg/thread/commit/56016e834896aa09df44bff6ff6d166a2d6e1f47  
https://github.com/boostorg/thread/commit/b6b6a6944a9526f49e70fe6d415e213c5b5f8824  
  
Please, could we move this discussion to https://github.com/boostorg/thread/pull/142  
  
Would you mind to report again all the results on this branch including windows?  
  
I would appreciate if you create a sub-branch of https://github.com/boostorg/thread/pull/142 with the changes you want to propose. The best is to have a PR for each issue you find.  
  
I've added Travis and AppVeyor CI so that we could see if the proposed changes break something.  
  
I'm wondering that now that we are running the test on virtual machines, we could run your test in those machines. If you don't know how to update the Jamfile, I could do it.

---

## Comment 110

> Username: viboes  
> Created_at: 2017-09-19 21:39:27 UTC  
> Url: https://github.com/boostorg/thread/pull/141#issuecomment-330681567  

I'm closing this PR. See https://github.com/boostorg/thread/pull/142

---
