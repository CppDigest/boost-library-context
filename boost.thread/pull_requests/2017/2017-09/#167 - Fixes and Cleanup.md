# #167 Fixes and Cleanup [Merged]

> Username: austin-beer  
> Created at: 2017-09-29 04:43:55 UTC  
> Updated at: 2020-03-21 20:31:50 UTC  
> Merged at: 2017-10-01 08:15:32 UTC  
> Closed at: 2017-10-01 08:15:32 UTC  
> Url: https://github.com/boostorg/thread/pull/167  

- Fixed build failures on Windows. The timespec struct is not supported by older versions of Visual Studio. I changed the internal representation inside of the *_timespec_timepoint classes to a boost::intmax_t representing the number of nanoseconds since the epoch.  
- Fixed some functions that wouldn't execute at all if they were provided a negative time duration or an absolute time that was in the past. From what I understand, they should instead execute once and then return immediately.  
- Moved pthread/timespec.hpp to detail/timespec.hpp.  
- Deleted detail/internal_clock.hpp and moved the seven relevant lines into detail/timespec.hpp. This keeps all of the internal clock declarations in one place.  
- Renamed thread_detail::internal_clock_t to detail::internal_chrono_clock to be consistent with and yet clearly differentiated from detail::internal_timespec_clock.  
- Removed "using namespace chrono" to eliminate ambiguious namespace resolution when referencing detail::internal_chrono_clock.  
- Re-enabled a few tests on Windows that had previously been disabled. I want to see whether or not they still need to be disabled.  
  
I'm hoping this PR will fix the AppVeyor failures that the last PR introduced. I have been building with Visual Studio 2015 but I switched over to Visual Studio 2010 to better catch issues like this in the future.

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2017-09-29 06:15:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/167#pullrequestreview-66066065  

📁 include/boost/thread/pthread/condition_variable.hpp

```diff
 112 |         int cond_res;
 113 |         {
 114 |+             timespec const& ts = timeout.getTs();
```

> Username: viboes  
> Created_at: 2017-09-29 06:15:13 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141796113  

I find this weird. if getTs() return now a timespec, why not just define a variable as in  
```  
const timespec ts =  timeout.getTs();  
```  
?  
  
I wonder if we shouldn't store timespec on pthread systems and intmax_t in windows in internal_timespec_duration. Would this change add a lot of of conditional compilation in detail/timespec .hpp?  
  
In addition it has no sense to name it timespec.hpp if it can be something else.  
  
What do you think?

> Username: viboes  
> Created_at: 2017-09-29 06:24:59 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141797039  

What about platform_clock, platform_duration and platform_timepoint?  
  
The file could be renamed to platform_time.hpp

> Username: austin-beer  
> Created_at: 2017-09-29 06:33:20 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141797916  

Agreed. It is weird. I'll change it.  
  
I think you're right that we should probably store timespec on pthread and intmax_t on Windows. That would be the most efficient. I don't think it will add too much conditional compilation.

> Username: austin-beer  
> Created_at: 2017-09-29 06:37:20 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141798311  

And I like the *platform* names instead of *timespec*.


---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-29 06:17:24 UTC  
> Updated_at: 2017-09-29 06:28:33 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333040575  

Ok, I will add an old version of msvc into AppVeyor to check this kind of backward compatibility issues.  
Maybe you can do it on your branch.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-29 06:31:29 UTC  
> Updated_at: 2017-09-29 06:31:40 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333042588  

There are a lot of errors in Windows and also on MacOs :(

---

## Comment 4

> Username: austin-beer  
> Created_at: 2017-09-29 06:31:53 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333042644  

Yep, I'm working on those as we speak.

---

## Review 5 [Commented]

> Username: viboes  
> Created_at: 2017-09-29 21:01:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/167#pullrequestreview-66270976  

📁 include/boost/thread/pthread/mutex.hpp

```diff
 269 |-           const timespec ts = timeout.getTs();
 270 |-           int const res=pthread_mutex_timedlock(&m,&ts);
 269 |+           int const res=pthread_mutex_timedlock(&m,&timeout.getTs());
```

> Username: viboes  
> Created_at: 2017-09-29 21:01:45 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141967608  

I find this weird. Passing the address of the returned value.

> Username: austin-beer  
> Created_at: 2017-09-29 21:12:22 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141969607  

That's what the original code did. getTs() passes a const reference so it's valid and saves an unnecessary copy of the variable.

> Username: viboes  
> Created_at: 2017-09-29 22:48:54 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141983071  

It us just I'm not used to see this kind of construct. It is just me.


---

## Review 6 [Commented]

> Username: viboes  
> Created_at: 2017-09-29 21:07:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/167#pullrequestreview-66272244  

📁 test/sync/mutual_exclusion/locks/unique_lock/cons/move_ctor_upgrade_lock_for_pass.cpp

```diff
  31 | int main()
  32 | {
  33 |+ #if defined(BOOST_THREAD_PLATFORM_PTHREAD)
```

> Username: viboes  
> Created_at: 2017-09-29 21:07:35 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141968711  

Now that win32 works with the pthread/shared_mutex generic implementation this should works. What was wrong?

> Username: austin-beer  
> Created_at: 2017-09-29 21:13:47 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141969860  

I'd have to go back and re-run it again to remember the exact error. It was something related to not being able to copy (or maybe move) between mutexes or locks of different types.

> Username: austin-beer  
> Created_at: 2017-09-29 21:26:53 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141972104  

Here's an example of the error that occurs in these tests with Visual Studio 2010. I'm not sure what the problem is. Do you have any ideas?  
  
```  
sync\mutual_exclusion\locks\unique_lock\cons\move_ctor_upgrade_lock_try_pass.cpp(34) : error C2664: 'boost::unique_lock<Mutex>::unique_lock<boost::try_to_lock_t>(Mutex &,const TimeDuration &)' : cannot convert parameter 1 from 'boost::upgrade_lock<Mutex>' to 'boost::upgrade_mutex &'  
        with  
        [  
            Mutex=boost::upgrade_mutex,  
            TimeDuration=boost::try_to_lock_t  
        ]  
        and  
        [  
            Mutex=boost::upgrade_mutex  
        ]  
```

> Username: austin-beer  
> Created_at: 2017-09-29 21:35:18 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141973376  

Oh, I see what's going on, although I'm not sure why. In `lock_types.hpp`, Visual Studio 2010 is choosing this constructor:  
```  
    template<typename TimeDuration>  
    unique_lock(Mutex& m_,TimeDuration const& target_time);  
```  
instead of this constructor:  
```  
    unique_lock(Mutex& m_, try_to_lock_t);  
```

> Username: austin-beer  
> Created_at: 2017-09-29 21:44:39 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141974978  

Oh.....in `lock_types.hpp`, this is the constructor it needs, but it's not available on Windows:  
  
```  
#if defined(BOOST_THREAD_PLATFORM_PTHREAD)  
    // Conversion from upgrade locking  
    unique_lock(BOOST_THREAD_RV_REF_BEG upgrade_lock<mutex_type> BOOST_THREAD_RV_REF_END ul, try_to_lock_t)  
    : m(0),is_locked(false)  
    {  
      if (BOOST_THREAD_RV(ul).owns_lock())  
      {  
        if (BOOST_THREAD_RV(ul).mutex()->try_unlock_upgrade_and_lock())  
        {  
          m = BOOST_THREAD_RV(ul).release();  
          is_locked = true;  
        }  
      }  
      else  
      {  
        m = BOOST_THREAD_RV(ul).release();  
      }  
    }  
#endif  
```

> Username: austin-beer  
> Created_at: 2017-09-29 21:45:53 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141975151  

I got it...the #if defines were added by you and I just failed to remove them. That's why the tests are failing. I'll take care of it. Sorry for the spam.

> Username: viboes  
> Created_at: 2017-09-29 22:51:06 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141983330  

No problem. I know that this is working now in develop, so I know we just did something wrong.


---

## Review 7 [Commented]

> Username: viboes  
> Created_at: 2017-09-29 21:08:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/167#pullrequestreview-66272557  

📁 include/boost/thread/detail/timespec.hpp

```diff
 139 | #if defined BOOST_THREAD_USES_DATETIME
 140 |-       real_timespec_timepoint(boost::system_time const& abs_time)
 140 |+       explicit real_timespec_timepoint(boost::system_time const& abs_time)
```

> Username: viboes  
> Created_at: 2017-09-29 21:08:53 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141968980  

Why explicit?

> Username: austin-beer  
> Created_at: 2017-09-29 21:15:29 UTC  
> Updated_at: 2017-09-29 21:54:07 UTC  
> Url: https://github.com/boostorg/thread/pull/167#discussion_r141970209  

I was getting an error awhile back related to Visual Studio 2010 not finding the correct constructor. But that may have been due to other issues that are now fixed. I can remove it again and see if it still works fine?


---

## Comment 8

> Username: viboes  
> Created_at: 2017-09-30 07:20:18 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333290054  

The issue from   
```  
libs/thread/test/sync/mutual_exclusion/sync_pq/pq_single_thread_pass.cpp(58): test 'diff < milliseconds(600) && diff > milliseconds(500)' failed in function 'void test_pull_for()'  
```  
  
is not your fault.  
  
The implementation/interface of sync_priority_queue is wrong. I'll try to fix it.  
  
Hrr, there are too much wrong time related interfaces in Boost.Thread.

---

## Comment 9

> Username: viboes  
> Created_at: 2017-09-30 07:26:37 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333290334  

I don't remember why I dded such time-related functions as the C++ proposal it is based on (http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0260r1.html) don't provides such functions.

---

## Comment 10

> Username: viboes  
> Created_at: 2017-09-30 07:27:50 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333290378  

If you believe your PR is ready to merge I'll do.

---

## Comment 11

> Username: viboes  
> Created_at: 2017-09-30 07:46:31 UTC  
> Updated_at: 2017-09-30 14:45:14 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333291275  

Could you give a last try with  
```  
  BOOST_TEST(diff < milliseconds(650) && diff > milliseconds(500));  
```  
  
I suspect that now that we could be pooling (at least in MacOs) waiting for 500ms could be very easily wait for more than 600ms.

---

## Comment 12

> Username: viboes  
> Created_at: 2017-10-01 19:48:09 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333401749  

As you see I've already merged your PR.

---

## Comment 13

> Username: austin-beer  
> Created_at: 2017-10-02 15:14:39 UTC  
> Url: https://github.com/boostorg/thread/pull/167#issuecomment-333565406  

Thanks. I've been out of the office all weekend so I wasn't able to take care of this. Thanks for taking a look at the pq_single_thread_pass issues.

---
