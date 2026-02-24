# #151 Updates to fix interruption-point inconsistencies. [Merged]

> Username: austin-beer  
> Created at: 2017-09-19 15:08:54 UTC  
> Updated at: 2017-09-19 21:12:51 UTC  
> Merged at: 2017-09-19 20:08:48 UTC  
> Closed at: 2017-09-19 20:08:48 UTC  
> Url: https://github.com/boostorg/thread/pull/151  

* Fixed the interruption-point versions of sleep_for/sleep_until() to always use condition variables.  
* Fixed the no-interruption-point versions of sleep_for/sleep_until() to use pthread_delay_np or nanosleep whenever possible.  
* Updated hidden::sleep_for() to always use a condition variable.  
* Updated no_interruption_point::hidden::sleep_for() to use pthread_delay_np or nanosleep whenever possible.  
  
NOTE: These changes also fix the two test failures with sleep_for/sleep_until when BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is disabled.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-09-19 18:42:47 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330633344  

Thanks for working on this.  
Please, could you share the results fro the 3 variants linu-mono linux-no-mono and windows?

---

## Review 2 [Commented]

> Username: viboes  
> Created_at: 2017-09-19 18:50:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/151#pullrequestreview-63764451  

📁 include/boost/thread/v2/thread.hpp

```diff
  24 | #ifdef BOOST_THREAD_USES_CHRONO
  25 | 
  26 |+ #if !defined BOOST_THREAD_SLEEP_FOR_IS_STEADY
```

> Username: viboes  
> Created_at: 2017-09-19 18:50:41 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139783190  

IIUC, we have now just two variants:  
* BOOST_THREAD_SLEEP_FOR_IS_STEADY  
* NOT BOOST_THREAD_SLEEP_FOR_IS_STEADY  
  
Could you rework the code to make this more evident.  
See other comments how this can be possible

> Username: austin-beer  
> Created_at: 2017-09-19 18:59:06 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139785485  

Yes, I can definitely do that. I was just trying to minimize code changes for the PR.


---

## Review 3 [Commented]

> Username: viboes  
> Created_at: 2017-09-19 18:53:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/151#pullrequestreview-63765246  

📁 include/boost/thread/v2/thread.hpp

```diff
  48 |         d = t - Clock::now();
  49 |       }
  50 |     }
```

> Username: viboes  
> Created_at: 2017-09-19 18:53:11 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139783898  

Add here the sleep_for. #99 #139


---

## Review 4 [Commented]

> Username: viboes  
> Created_at: 2017-09-19 18:53:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/151#pullrequestreview-63765313  

📁 include/boost/thread/v2/thread.hpp

```diff
  53 |+ // Use pthread_delay_np or nanosleep whenever possible here in the no_interruption_point
  54 |+ // namespace because they do not provide an interruption point.
  55 |+ #if defined BOOST_THREAD_SLEEP_FOR_IS_STEADY
```

> Username: viboes  
> Created_at: 2017-09-19 18:53:27 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139783949  

Use #else instead


---

## Review 5 [Commented]

> Username: viboes  
> Created_at: 2017-09-19 19:03:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/151#pullrequestreview-63768255  

📁 src/pthread/thread.cpp

```diff
 448 |-     #   elif defined(BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC)
 450 |+     #   else
 451 |+                   // Fall back to using a condition variable even though it does provide an interruption point.
```

> Username: viboes  
> Created_at: 2017-09-19 19:03:27 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139786587  

IIUC, this function is called only in the BOOST_THREAD_SLEEP_FOR_IS_STEADY case and so the #else never happens, isn't it?

> Username: austin-beer  
> Created_at: 2017-09-19 19:05:35 UTC  
> Updated_at: 2017-09-19 19:19:06 UTC  
> Url: https://github.com/boostorg/thread/pull/151#discussion_r139787094  

Yes, I believe that is correct. The #else should never get executed.


---

## Comment 6

> Username: austin-beer  
> Created_at: 2017-09-19 19:31:44 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330649012  

Here are the test results from this PR.  
  
[linux_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1315574/linux_results_austin_no_monotonic.txt)  
[linux_results_austin_yes_monotonic.txt](https://github.com/boostorg/thread/files/1315575/linux_results_austin_yes_monotonic.txt)  
[windows_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1315576/windows_results_austin_no_monotonic.txt)  
  
The following two failures in the no-monotonic test are unavoidable. This is because hidden::sleep_for() has to use a condition variable (so that it has an interruption point), but it doesn't have access to a monotonic clock to see how much time has passed (unlike the sleep_for() that takes a Chrono time).  
  
```  
boost::this_thread::sleep(), posix duration:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
  
 boost::this_thread::sleep(), no thread, posix duration:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
```

---

## Comment 7

> Username: viboes  
> Created_at: 2017-09-19 19:47:14 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330652871  

Thanks.  
  
Shouldn't boost::this_thread::sleep(), posix duration: waits for 100ms? this will reduce the gap, isn't it?

---

## Comment 8

> Username: austin-beer  
> Created_at: 2017-09-19 19:51:24 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330653936  

I'm afraid I don't quite understand your question. Could you rephrase the question?

---

## Comment 9

> Username: viboes  
> Created_at: 2017-09-19 20:03:21 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330657092  

The current definition is  
  
```  
        template<typename TimeDuration>  
        inline BOOST_SYMBOL_VISIBLE void sleep(TimeDuration const& rel_time)  
        {  
          boost::this_thread::hidden::sleep_for(detail::timespec_duration(rel_time));  
        }  
```  
  
Instead of using `boost::this_thread::hidden::sleep_for`, why not pool on a condition as we do in  
https://github.com/boostorg/thread/pull/151/commits/f1dec7935fc2198702a2a5acf5512838628b384f#diff-52550ac33fe611a20ce1f27ce707309cR141

---

## Comment 10

> Username: viboes  
> Created_at: 2017-09-19 20:07:46 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330658289  

I'll take this PR already, as aside the posix-time all in linux is ok.  
  
BTW,   
  
I've see a regression on windows.  
  
Before  
```  
boost::this_thread::sleep_until(), system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 592 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 826 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 373 ms, Returned: timeout, Passed  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 217 ms, Returned: timeout, Passed  
boost::this_thread::sleep_until(), custom time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 592 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 841 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 357 ms, Returned: timeout, Passed  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 233 ms, Returned: timeout, FAILED: TOO LONG  
```  
  
After  
```  
boost::this_thread::sleep_until(), system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 607 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 841 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 358 ms, Returned: timeout, Passed  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 248 ms, Returned: timeout, FAILED: TOO LONG  
boost::this_thread::sleep_until(), custom time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 592 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 810 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 358 ms, Returned: timeout, Passed  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 124 ms, Returned: timeout, Passed  
```

---

## Comment 11

> Username: austin-beer  
> Created_at: 2017-09-19 20:16:36 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330660573  

I'm not sure if that is a true regression on Windows or not. I'm running all of my windows tests in a VM, and so I've seen up to 50 milliseconds of variability in the test results, which sometimes causes false results. I do have a dedicated Windows machine that I can use, but I don't have easy access to it at the moment. Once we start focusing on the Windows side of things, I plan to move to running my tests on that system.

---

## Comment 12

> Username: austin-beer  
> Created_at: 2017-09-19 20:22:05 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330661978  

> The current definition is  
>   
>         template<typename TimeDuration>  
>         inline BOOST_SYMBOL_VISIBLE void sleep(TimeDuration const& rel_time)  
>         {  
>           boost::this_thread::hidden::sleep_for(detail::timespec_duration(rel_time));  
>         }  
> Instead of using boost::this_thread::hidden::sleep_for, why not pool on a condition as we do in  
> f1dec79#diff-52550ac33fe611a20ce1f27ce707309cR141  
  
We're able to poll every 100 milliseconds in other places in the code because we have a steady clock to compare against. In this function, all we have is the internal clock. When the internal clock is the system clock, there's no way to know when the system clock jumps, and so there's no way to wait the correct amount of time.

---

## Comment 13

> Username: viboes  
> Created_at: 2017-09-19 20:30:21 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330664089  

Right, we are able to implement `sleep_for(chrono::duration)` because we have a steady::clock. posix-duration is another kind of duration, but we don't have a Date-Time steady clock.    
If we have BOOST_CHRONO we should be able to implement convert to chrono::duration, isn't it?  
It will left the case when the user uses Boost.DateTime and not use Boost.Chrono.  I believe this is a really minor set of users.  
What do you think?

---

## Comment 14

> Username: viboes  
> Created_at: 2017-09-19 20:31:47 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330664450  

Don't worry for the Windows possible regression. You are surely right. I've observed much more  differences in the past on Windows.

---

## Comment 15

> Username: austin-beer  
> Created_at: 2017-09-19 20:42:34 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330667470  

Yes, if we have BOOST_THREAD_USES_CHRONO we could convert to chrono::duration and that would fix the issue. And if BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC is defined that already fixes the issue. I'm not sure if it would be good to start mixing Chrono functions with DateTime functions. I like the fact that, currently, the two are completely separate from each other.  
  
But most users do have BOOST_THREAD_USES_CHRONO and don't have BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC, so in that situation we should probably make sleep() work correctly by using chrono::duration under the covers.

---

## Comment 16

> Username: viboes  
> Created_at: 2017-09-19 20:51:44 UTC  
> Url: https://github.com/boostorg/thread/pull/151#issuecomment-330669731  

I agree that it would be desirable that Chrono and DateTime code stay independent, but from a pragmatic point of view, it would be a pity to don't provide the correct functionality when we have Boost.Chrono.

---
