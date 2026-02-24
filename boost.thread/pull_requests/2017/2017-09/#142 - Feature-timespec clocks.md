# #142 Feature/timespec clocks [Merged]

> Username: viboes  
> Created at: 2017-09-06 22:36:35 UTC  
> Updated at: 2018-02-16 16:20:53 UTC  
> Merged at: 2018-02-16 06:10:42 UTC  
> Closed at: 2018-02-16 06:10:42 UTC  
> Url: https://github.com/boostorg/thread/pull/142  

Branch to fix the time related issues with non-steady clocks.  
  
Thanks to Austin Beer for his work on PR https://github.com/boostorg/thread/pull/141 and helping me to test this branch.

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-09-19 04:48:48 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330428477  

Hi Vicente,  
  
Here is the latest test file and the results against the latest test code in this branch.  
  
I added a "long" time jump forward in addition to the existing "short" time jump forward to test how the functions behave when the time jumps forward past the time when they are supposed to expire.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1313065/test_boost_sleep.cpp.txt)  
[linux_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1313066/linux_results_fixed_no_monotonic.txt)  
[linux_results_fixed_yes_monotonic.txt](https://github.com/boostorg/thread/files/1313067/linux_results_fixed_yes_monotonic.txt)  
[windows_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1313068/windows_results_fixed_no_monotonic.txt)  
[windows_results_fixed_yes_monotonic.txt](https://github.com/boostorg/thread/files/1313069/windows_results_fixed_yes_monotonic.txt)  
  
These tests require root (Linux) or administrator (Windows) privileges in order to run, so they can change the system time. With that limitation would you still be able to run these tests on your virtual machines? I don't know how to update the Jamfile so I would need some help with that.  
  
I'm working on sending you a pull request using a sub-branch of this branch with some more changes I would like to propose.

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-09-19 05:38:01 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330434707  

In src/pthread/thread.cpp, why does hidden::sleep_for() use a condition variable when get_current_thread_data() is not NULL but uses no_interruption_point::hidden::sleep_for() when it is NULL? The condition variable in thread_info doesn't seem to be special. It seems to me the condition variable could simply be a local variable that is used regardless of whether or not thread_info is NULL, but perhaps there's something I'm missing.

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-09-19 05:39:39 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330434937  

And thanks for letting me know that sleep_for/sleep_until are interruption points and therefore must use a condition variable. I didn't fully understand that.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-09-19 07:12:16 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330450638  

BTW, all timed-based functions are (or should be) documented as interruption points except the sleep inside the `no_interruption_point` namespace and as consequence must use a condition variable

---

## Comment 5

> Username: viboes  
> Created_at: 2017-09-19 07:21:59 UTC  
> Updated_at: 2017-09-19 07:25:35 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330452658  

I'll try to fix first the no-monotonic issues for chrono related API  
  
```  
boost::this_thread::sleep_for():  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
boost::this_thread::sleep_until(), steady time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 351 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
  
boost::thread::try_join_for():  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
  
boost::shared_mutex::try_lock_for():  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
  
boost::shared_mutex::timed_lock(), posix duration:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
  
boost::upgrade_mutex::try_lock_for():  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 580 ms, Actual: 810 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (short): Expected: 580 ms, Actual: 350 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (long):  Expected: 580 ms, Actual: 110 ms, Returned: timeout, FAILED: TOO SHORT  
```  
  
Maybe you concentrate if you want on the DateTime issues for the monotonic case  
  
```  
boost::thread::try_join_until(), system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 810 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
boost::thread::try_join_until(), custom time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 810 ms, Returned: timeout, Passed  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::thread::timed_join(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::condition_variable::timed_wait(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
boost::condition_variable::timed_wait(), with predicate, posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::condition_variable_any::timed_wait(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::condition_variable_any::timed_wait(), with predicate, posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::shared_mutex::timed_lock(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::upgrade_mutex::timed_lock(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::timed_mutex::timed_lock(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
  
boost::recursive_timed_mutex::timed_lock(), posix system time:  
    While system clock remains stable:        Expected: 580 ms, Actual: 580 ms, Returned: timeout, Passed  
    While system clock jumps back:            Expected: 810 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO SHORT  
    While system clock jumps forward (short): Expected: 350 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
    While system clock jumps forward (long):  Expected: 110 ms, Actual: 580 ms, Returned: timeout, FAILED: TOO LONG  
```  
   
I'll have to manage with the DateTime issues for the non-monotonic case and the windows issues.  
  
I'll add also a branch for my modifications. It would be great to have short PR.  
  
let me know if you have a better idea or how to organize the work.

---

## Comment 6

> Username: austin-beer  
> Created_at: 2017-09-19 15:13:18 UTC  
> Updated_at: 2017-09-19 15:13:35 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330572226  

Your plan sounds good. I don't have any better ideas about how to organize the work. If we can get the feature/timespec_clocks branch into a well-tested state where all of the tests pass, then I think we'll be good.  
  
As I have time I'll work on the DateTime issues in the monotonic case and create small PRs for each issue.  
  
I went ahead and sent you PR https://github.com/boostorg/thread/pull/151 just because I'd already started working on it. If it overlaps with anything you're already working on, feel free to ignore it. Thanks.

---

## Comment 7

> Username: austin-beer  
> Created_at: 2017-09-20 19:18:36 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330953541  

I added a long system clock jump backwards to each test, and included the results below. Without a monotonic clock, some of these new tests fail, but there's nothing we can do about that. When a condition variable is waiting 100 milliseconds on the system clock, and the system clock jumps backwards by 900 milliseconds, the condition variable won't return for 1 full second. So even when we're polling every 100 milliseconds, we can still end up waiting too long, which is why some of these new tests fail.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1318942/test_boost_sleep.cpp.txt)  
[linux_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1318944/linux_results_fixed_no_monotonic.txt)  
[linux_results_fixed_yes_monotonic.txt](https://github.com/boostorg/thread/files/1318941/linux_results_fixed_yes_monotonic.txt)  
[windows_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1318943/windows_results_fixed_no_monotonic.txt)

---

## Comment 8

> Username: viboes  
> Created_at: 2017-09-20 20:27:46 UTC  
> Updated_at: 2017-09-20 20:28:38 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330970873  

If we are able to do a sleep_until  to a system time correctly in any jumping case (no-mono), sleep_for and sleep_until a steady time could be done right using the sleep_until  to a system time, isn't it?

---

## Comment 9

> Username: viboes  
> Created_at: 2017-09-20 20:30:03 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330971475  

I mean that we should pool on system_clock::now() + milliseconds(100).

---

## Comment 10

> Username: austin-beer  
> Created_at: 2017-09-20 20:33:13 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-330972356  

No, that won't work. sleep_until() with a system time works correctly only because it's required by the contract to wait longer when the system clock jumps backward. But sleep_for() and sleep_until() with a steady time are required to _not_ wait longer, even when the system clock jumps backwards. So even if we poll on `system_clock::now() + milliseconds(100)` we will still end up waiting longer than we should.

---

## Comment 11

> Username: viboes  
> Created_at: 2017-09-21 05:47:05 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331057598  

Right.  
However, I remember you said that waiting on a timed condition would finish when the system time is changed. IIUC, this is not the case when waiting on a system time.

---

## Comment 12

> Username: austin-beer  
> Created_at: 2017-09-21 15:10:57 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331187342  

> Right.  
> However, I remember you said that waiting on a timed condition would finish when the system time is changed. IIUC, this is not the case when waiting on a system time.  
  
Ah, yes, I do remember saying that. What I failed to mention is that that is only true when the system clock jumps _forward_ and crosses the timeout point. It is not true when the system clock jumps _backward_.  
  
This is the one situation where the only solution is to enable BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC.

---

## Comment 13

> Username: austin-beer  
> Created_at: 2017-09-21 22:53:52 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331303709  

Thanks for reviewing and merging all of my updates so far. I have some other things that I need to focus on for the next week or two, so I don't plan on submitting any more PRs until I free up again. However, I will still be available to review and/or test any updates you make to the timespec_clocks branch.  
  
Looking forward to hopefully seeing these updates in Boost 1.66 in December!

---

## Comment 14

> Username: viboes  
> Created_at: 2017-09-21 22:57:09 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331304203  

I'll try to do my best, while you be busy ;-)  
  
We will surely have all this fixed for Boost 1.66.

---

## Comment 15

> Username: viboes  
> Created_at: 2017-09-22 02:19:13 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331332173  

I've committed a minor change to try to fix condition_variable::timed_wait for linux.  
The code specific to BOOST_THREAD_HAS_CONDATTR_SET_CLOCK_MONOTONIC has not been compiled :(  
  
Please, could you check my changes and report the results.   
  
I've added a fixme, because I don't understand why the commented change doesn't work. Any help appreciated.

---

## Comment 16

> Username: viboes  
> Created_at: 2017-09-22 02:20:10 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331332304  

Bad news: we need tests for time-related boost::future/shared_future functions also :(

---

## Comment 17

> Username: austin-beer  
> Created_at: 2017-09-23 01:07:53 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331595101  

Do we need to add tests for v2/shared_mutex.hpp and/or for pthread/shared_mutex_assert.hpp? It doesn't look like either are being used currently, but both contain time-related code.

---

## Comment 18

> Username: viboes  
> Created_at: 2017-09-23 04:26:51 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331609747  

v2/shared_mutex.hpp can be used on windows but I I believe I should remove pthread/shared_mutex_assert.hpp .

---

## Comment 19

> Username: austin-beer  
> Created_at: 2017-09-24 21:32:57 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331741722  

I finally moved all of the test code and the latest results onto GitHub. Here are the latest results with the updates you've made so far.  
  
https://github.com/austin-beer/test-time-jumps

---

## Comment 20

> Username: viboes  
> Created_at: 2017-09-24 21:54:37 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331742910  

It is a pleasure to work with you.  
  
Thanks so much for all your contributions and your reports.

---

## Comment 21

> Username: austin-beer  
> Created_at: 2017-09-24 21:56:22 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331742995  

You're very welcome. I'm glad to help.

---

## Comment 22

> Username: viboes  
> Created_at: 2017-09-24 22:00:13 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331743192  

I have had some issues with the shared_mutex implementation on windows.  
I've moved to the incomplete v2/shared_mutex.hpp version for linux for the time being.  
The win32 version introduces a lot of regressions on the generic regression tests.

---

## Comment 23

> Username: viboes  
> Created_at: 2017-09-24 22:33:20 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331744924  

If we apply the same kind of fixes that we have applied so far for future (which is generic), we will have the same issue that I have found on windows for shared_mutex (which was also generic in some way) as we don't have the equivalent of the timespec_clocks.   
  
Temporally I could duplicate the concerned parts of the file and fix futures on pthread. But we need some kind of timespec_clock for windows to be able to fix all the other files.

---

## Comment 24

> Username: austin-beer  
> Created_at: 2017-09-24 22:56:39 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331746073  

I think we just need to update timespec.hpp so that real_timespec_clock::now() and mono_timespec_clock::now() work on Windows as well as Linux. Hopefully we can just copy the necessary code from Boost Chrono. Then we can move timespec.hpp out of the pthread directory since it will be common to both.

---

## Comment 25

> Username: austin-beer  
> Created_at: 2017-09-24 23:07:31 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331746614  

FYI, https://github.com/boostorg/thread/pull/142/commits/aa221d02b5e7b058d18299c46919e7bea0a4fcd7 fixed the tests it was supposed to fix.  
  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_fixed_yes_monotonic.txt

---

## Comment 26

> Username: austin-beer  
> Created_at: 2017-09-25 15:46:22 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331923830  

Updated test results. All Linux test results that are passable now pass, including futures. No change on the Windows results.  
  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_fixed_no_monotonic.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_fixed_yes_monotonic.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_fixed_no_monotonic.txt

---

## Comment 27

> Username: austin-beer  
> Created_at: 2017-09-25 15:58:23 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331927642  

FYI, the current Windows test is running without BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN or BOOST_THREAD_PROVIDES_SHARED_MUTEX_UPWARDS_CONVERSIONS defined, so it's not using the POSIX implementation. If you need me to run the Windows test with those defined, let me know.

---

## Comment 28

> Username: viboes  
> Created_at: 2017-09-25 16:28:58 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331936942  

Great. We achieved the first goal.  
  
Now we need to fix the windows failures (functions returning no-timeout) and improve the QOI.  
  
BTW, I believe that you need to define BOOST_THREAD_DONT_PROVIDE_GENERIC_SHARED_MUTEX_ON_WIN if you don't  want the generic shared mutex, and I don't see this on the Makefile.  
  
```  
// GENERIC_SHARED_MUTEX_ON_WIN  
#if ! defined BOOST_THREAD_DONT_PROVIDE_GENERIC_SHARED_MUTEX_ON_WIN \  
 && ! defined BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN  
#define BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN  
#endif  
```  
  
The same for   
```  
#if ! defined BOOST_THREAD_DONT_PROVIDE_SHARED_MUTEX_UPWARDS_CONVERSIONS \  
 && ! defined BOOST_THREAD_PROVIDES_SHARED_MUTEX_UPWARDS_CONVERSIONS  
#define BOOST_THREAD_PROVIDES_SHARED_MUTEX_UPWARDS_CONVERSIONS  
#endif  
```

---

## Comment 29

> Username: austin-beer  
> Created_at: 2017-09-25 16:33:50 UTC  
> Updated_at: 2017-09-25 16:34:09 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331938310  

I'm using the default BOOST_THREAD_VERSION, which is 2. That version doesn't automatically define BOOST_THREAD_PROVIDES_GENERIC_SHARED_MUTEX_ON_WIN or BOOST_THREAD_PROVIDES_SHARED_MUTEX_UPWARDS_CONVERSIONS, so I have to explicitly define them if I want them.

---

## Comment 30

> Username: austin-beer  
> Created_at: 2017-09-25 17:24:30 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-331952285  

Here, these test results might be more helpful on Windows.  
  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_fixed_no_generic_shared.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_fixed_yes_generic_shared.txt

---

## Comment 31

> Username: austin-beer  
> Created_at: 2017-09-28 03:15:42 UTC  
> Updated_at: 2017-09-28 15:16:22 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-332716012  

Here are the list of changes that I believe need to be made to allow the generic shared_mutex.hpp and future.hpp to work with the win32 code using the updated classes in timespec.hpp.  
  
- Replace the two `do_wait()` functions in `win32/condition_variable.hpp` with public `do_wait_until()` functions that have identical signatures to their counterparts in `pthread/condition_variable_fwd.hpp`. The new functions would all have `internal_timespec_timepoint` parameters.  
- Replace `basic_cv_list_entry::wait()` in `win32/condition_variable.hpp` with `basic_cv_list_entry::do_wait_until()` that has an `internal_timespec_timepoint` parameter.  
- Update `interruptible_wait()` and `non_interruptible_wait()` in `src/win32/thread.cpp` to have an `internal_timespec_timepoint` parameter.  
- Delete all of the other unused overloads for `interruptible_wait()` and `non_interruptible_wait()` in `win32/thread_data.hpp`.  
- Change `do_try_join_for_noexcept()` in `src/win32/thread.cpp` back to `do_try_join_until_noexcept()` that has an `internal_timespec_timepoint` parameter. This makes it identical to the `do_try_join_until_noexcept()` function in `src/pthread/thread.cpp`.  
- Update `detail/thread.hpp` to use the pthread join code for win32 as well, since there will no longer be any need for separate code paths for each.  
  
I may have some time in the next week or so to work on this, if you don't get to it first.

---

## Comment 32

> Username: viboes  
> Created_at: 2017-10-01 19:59:54 UTC  
> Updated_at: 2017-10-01 20:00:09 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333402478  

Please Austin, could you run the test once again.   
  
I will try to fix the remaining issues on `timed_mutex`/`recursive_timed_mutex` and `shared_mutex` for windows. Once we have those fixed, I will merge this PR.  
  
If you could have time to add tests for `sync_queue` time related functions we could as well fix the possible issues.

---

## Comment 33

> Username: austin-beer  
> Created_at: 2017-10-02 15:27:16 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333569588  

When I get the chance I'll run the tests again and add the sync_queue tests. I'm having trouble building on Linux ever since the threadapi commits. I'm hoping the solution is just to pull in newer versions of some of the other libraries in my boost workspace. Here's the error I'm getting in case you already know what the issue is:  
  
```  
Performing configuration checks  
  
    - 32-bit                   : no  
    - 64-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
    - symlinks supported       : yes  
    - lockfree boost::atomic_flag : yes  
error: Name clash for '<pstage/lib>libboost_system.a'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompatible properties:  
error:   
error:     -  none  
error:     -  <threadapi>pthread  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
```

---

## Comment 34

> Username: austin-beer  
> Created_at: 2017-10-02 15:35:58 UTC  
> Updated_at: 2017-10-02 16:58:10 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333572335  

Also, you may want to hold off on merging this PR for awhile. I discovered on Friday that our changes (specifically the polling every 100 milliseconds in condition_variable) have introduced a "lost wakeup" bug in condition_variable. The reason is due to the following in http://pubs.opengroup.org/onlinepubs/9699919799/functions/pthread_cond_timedwait.html:  
  
`It is important to note that when pthread_cond_wait() and pthread_cond_timedwait() return without error, the associated predicate may still be false. Similarly, when pthread_cond_timedwait() returns with the timeout error, the associated predicate may be true due to an unavoidable race between the expiration of the timeout and the predicate state change.`  
  
When we're polling every 100 milliseconds, and pthread_cond_timedwait times out (or the equivalent on Windows), we're assuming that the predicate has _not_ changed and so we're going ahead and polling for another 100 milliseconds. But because of the above statement that is not a valid assumption.  
  
Because of this, I think some of our changes need to be reverted. But not all of the changes need to be reverted. Only the ones where we don't have access to the predicate (i.e. some of the wait_*() functions in condition_variable). All of the sleep*(), try_join*(), and try_lock*() functions should still be fine because we have access to the predicate in those situations. We just need to review each one to make sure we haven't introduced the "lost wakeup" bug into them either.

---

## Comment 35

> Username: viboes  
> Created_at: 2017-10-02 16:58:26 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333596267  

Austin, I agree with you that we could test the predicate each time the pool expires, but I don't this as a defect but a QOI.   
  
Fortunately we don't have a lot of time related function with predicated. IIRC ony condition_variables support them.

---

## Comment 36

> Username: viboes  
> Created_at: 2017-10-02 17:02:44 UTC  
> Updated_at: 2017-10-02 17:02:52 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333597536  

I've followed the <threadapi> PR. Sorry for the inconveniences. I've decided to merge develop as often as it merits to not be too far when we will be ready to merge this branch.   
  
Please try by doing a pull of all the libraries on master and let me know what do you get.  
  
Alternatively remove bin.v2 and build all.

---

## Comment 37

> Username: viboes  
> Created_at: 2017-10-02 17:06:14 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333598448  

BTW, the sync_queue issue is due to the elapsed time been a little bit less than the required.  
  
```  
elapsed = 492676000ms  
libs\thread\test\sync\mutual_exclusion\sync_pq\pq_single_thread_pass.cpp(59): test 'diff < milliseconds(650) && diff >= milliseconds(500)' failed in function 'void __cdecl test_pull_for(void)'  
```  
  
I suspect that there was a time slicing between the now for the start and the pull_for. I will test for > 400ms only.

---

## Comment 38

> Username: austin-beer  
> Created_at: 2017-10-02 17:19:52 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333602145  

Ok, I'll try updating to master and seeing what happens.  
  
As far as the "lost wakeup" bug is concerned, is the following behavior acceptable?  
  
1. Thread A calls cond.wait_for(10 seconds)  
2. 1 second later, thread B calls cond.notify_one() or cond.notify_all()  
3. Thread A misses the notification and waits the full 10 seconds before returning  
  
Right now with our changes this behavior is possible, but I believe it shouldn't be.

---

## Comment 39

> Username: austin-beer  
> Created_at: 2017-10-02 19:32:42 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333640400  

I removed bin.v2, made sure I'd checked out and pulled the latest boost/master, and updated all submodules to the SHA-1s specified in boost/master. That builds fine. But as soon as I check out thread/develop into libs/thread, I get the same threadapi error. I also checked out the latest develop branch of each submodule to see if that would help, but it did not. I still got the same error. Something must be different between the way you're building and the way I'm building. Thanks in advance for any help you can provide!

---

## Comment 40

> Username: viboes  
> Created_at: 2017-10-02 19:49:29 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333644666  

How "Thread A misses the notification and waits the full 10 seconds before returning"?

---

## Comment 41

> Username: viboes  
> Created_at: 2017-10-02 19:52:42 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333645542  

From my side I just run the tests from the test directory as  
   
```  
b2 toolset=<tool> -jn -l300  
```  
  
How do you build Boost.Thread?

---

## Comment 42

> Username: austin-beer  
> Created_at: 2017-10-02 19:53:05 UTC  
> Updated_at: 2017-10-02 19:55:37 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333645659  

If thread A is right at the end of one of its 100 millisecond loops when notify_*() is called, do_wait_until() will return that a timeout occurred even though it received the signal from pthread_cond_signal() or pthread_cond_broadcast(). In our loop, if do_wait_until() reports that a timeout occurred (returns false), we go around and start waiting for another 100 milliseconds.

---

## Comment 43

> Username: austin-beer  
> Created_at: 2017-10-02 19:54:34 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333646079  

I use the following command to build just the libraries that I need for my test application:  
  
```  
b2 --build-dir=dist --with-chrono --with-thread --with-system toolset=gcc link=static variant=release stage  
```

---

## Comment 44

> Username: viboes  
> Created_at: 2017-10-02 19:55:35 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333646370  

I'll try the command you use myself on a new repository.

---

## Comment 45

> Username: austin-beer  
> Created_at: 2017-10-02 19:56:16 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333646566  

I also run the following first, just FYI:  
  
```  
b2 headers toolset=gcc  
```

---

## Comment 46

> Username: viboes  
> Created_at: 2017-10-02 20:00:29 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333647645  

But if "do_wait_until() reports that a timeout occurred (returns false)" is because the thread has not received the signal. Or are you talking of the case where the signal was sent just after the thread received a timeout and before calling to the wait again? If this is the case, I believe this is the normal behavior for wait_until/wait_for without a predicate. However for the signatures with a predicate we should evaluate the predicate after each timeout. Am I missing something evident?

---

## Comment 47

> Username: austin-beer  
> Created_at: 2017-10-02 20:13:52 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333651165  

Yes, I'm talking about the case where the signal was sent just after the thread received a timeout but before calling wait again. And yes, that is normal behavior. The problem is because we call do_wait_until() multiple times within a single call to wait_until(). For example:  
  
1. User calls `wait_until(1 second)`  
2. We call `do_wait_until(100 milliseconds)`  
3. Right after `do_wait_until()` times out, a signal is sent  
4. `do_wait_until()` returns a timeout to `wait_until()`  
5. `wait_until()` sees that `do_wait_until()` timed out, assumes that no signal was received, and calls `do_wait_until(100 milliseconds)` again **instead of returning to the user**  
  
So as far as the user is concerned the signal was lost, even though `do_wait_until()` behaved normally.

---

## Comment 48

> Username: viboes  
> Created_at: 2017-10-02 20:40:17 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333658248  

Well, the problem is that we don't know if the user predicate has changed or not, and the time has not been elapsed or reached, and IMO we can not return from the `wait_until`/`wait_for` function as we don't know if the signal was lost or not. We could of course respond as if it was the case, but then what is the added value of having a duration or timepoint parameter? The user has asked to `wait_until` the condition has been notified or the time is reached. Our algorithm works well when the notification is done while we are waiting and when the time is reached.   
  
  
IIRC, we have added the polling to ensure to be as close to the timeout. Maybe this was an error, and we should prioritize to don't loss notifications :(  
  
If we have a steady condition variable, it is not necessary to pool if the user is waiting for a steady timepoint  and it is better to don't do it. Are we polling in this case?  
  
If however the user requests to wait for a system clock, and we poll/don't poll, what are the possible behaviors when the system time change?   
  
When we don't have steady conditions, what are the possible behavior if we poll/don't poll?

---

## Comment 49

> Username: viboes  
> Created_at: 2017-10-02 20:44:33 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333659367  

Have you tried adding `threadapi=pthread` to the command line on linux?

---

## Comment 50

> Username: austin-beer  
> Created_at: 2017-10-02 20:53:39 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333661861  

`threadapi=pthread` on the command line fixes the issue for me. Thank you.

---

## Comment 51

> Username: austin-beer  
> Created_at: 2017-10-02 21:04:26 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333664711  

> Well, the problem is that we don't know if the user predicate has changed or not, and the time has not been elapsed or reached, and IMO we can not return from the wait_until/wait_for function as we don't know if the signal was lost or not.  
  
Yes, that is the problem.  
  
> IIRC, we have added the polling to ensure to be as close to the timeout. Maybe this was an error, and we should prioritize to don't loss notifications :(  
  
Yes, that's why we added the polling. I think we need to prioritize not losing notifications over being close to the timeout. The former is a contract issue. The latter is a QOI issue.  
  
However, we only need to do this for situations where _both_ of the following are true:  
  
1. we don't have access to the predicate  
2. the user-provided time is different from the internal clock  
  
For all other situations we either don't need to poll or we can continue polling like we are now. So this issue only affects a subset of our test cases. But for those test cases that are affected, I don't see any solution. The best we can do is make sure that our return value is no_timeout when we don't wait long enough.  
  
Can you think of any better solution?

---

## Comment 52

> Username: viboes  
> Created_at: 2017-10-02 21:22:55 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333669349  

> threadapi=pthread on the command line fixes the issue for me. Thank you.  
  
Glad to hear that. However I have introduced a regression :(

---

## Comment 53

> Username: viboes  
> Created_at: 2017-10-02 21:33:15 UTC  
> Updated_at: 2017-10-02 21:34:18 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333671761  

Ok. Granted we shouldn't loss notifications.  
  
Wondering if you have time to add tests that check that we don't loss signal. This will almost duplicate all the current tests.  
  
Hrr, as the loss is accidental, there will be no too much chances to catch the errors :(

---

## Comment 54

> Username: austin-beer  
> Created_at: 2017-10-02 21:34:23 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333672017  

When does this need to be merged by in order to make it into 1.66?

---

## Comment 55

> Username: viboes  
> Created_at: 2017-10-02 21:39:40 UTC  
> Updated_at: 2017-10-02 21:42:56 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333673158  

We have yet 3 weeks. See the release planning http://www.boost.org/development/index.html.  
  
While I would like to deliver it for boost-1.66, I prefer to deliver something that works correctly than delivering something that works most of the times or sometimes as it is now.  
  
Nevertheless I'm confident that this branch has a better quality than the develop branch, so there is always the temptation to deliver.

---

## Comment 56

> Username: austin-beer  
> Created_at: 2017-10-02 23:56:09 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333698540  

I wholeheartedly agree. I want to be very, very sure that we're not introducing any regressions and that everything works correctly before we release it. If that takes longer than three weeks, then I think that's fine.  
  
Here are the latest results. A lot of the Windows tests are failing again because I commented out some of the Windows changes in the last PR in order to avoid the lost notifications bug, which was causing the test_condition.cpp test to fail (which is how I discovered the issue in the first place).  
  
https://github.com/austin-beer/test-time-jumps  
  
All of the sleep(), try_join*(), and try_lock*() functions, as well as the functions in future.hpp, should still be fixable because we have access to the predicate in those situations. It's only the non-predicate condition_variable::wait_*() functions that we can't fix.

---

## Comment 57

> Username: austin-beer  
> Created_at: 2017-10-03 20:21:30 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333966321  

Just FYI, I've started working on a PR to address the lost notifications issue. I should have it ready in a few days.

---

## Comment 58

> Username: viboes  
> Created_at: 2017-10-03 20:53:40 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-333975113  

Perfet

---

## Comment 59

> Username: austin-beer  
> Created_at: 2017-10-23 15:38:55 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-338700851  

A project for my day job is due at the end of this week and so that's been taking up all of my time recently. However, I'm _hoping_ to be able to carve out some time this week to finish this PR. Here are the files that still need work.  
  
```  
win32/basic_timed_mutex.hpp  
win32/basic_recursive_mutex.hpp  
win32/shared_mutex.hpp  
v2/shared_mutex.hpp  
concurrent_queues/sync_timed_queue.hpp  
concurrent_queues/detail/sync_queue_base.hpp  
concurrent_queues/detail/sync_deque_base.hpp  
```  
  
One other issue is that `mono_platform_clock::now()` isn't yet implemented on Mac OS. Is that something you would have time to work on? I don't have access to a Mac OS platform to test on.

---

## Comment 60

> Username: viboes  
> Created_at: 2017-10-23 16:55:01 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-338724777  

Hi Austin, glad to hear you again.  
  
Yes, I'd have some time this week and then I 'll be on vacations for a week :)

---

## Comment 61

> Username: viboes  
> Created_at: 2017-10-23 20:42:20 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-338790257  

I've implemented mono_platform_clock and mono_platform_timepoint fro MacOs.   
  
https://github.com/boostorg/thread/commit/6d7bc558d0850964c448082bf2b5bec61a0870ae

---

## Comment 62

> Username: austin-beer  
> Created_at: 2017-11-28 15:46:43 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-347565637  

Hi Vicente,  
  
I just wanted to let you know that I haven't forgotten about this PR. I still plan to finish it up when I get the chance. Since we missed the 1.66 deadline, I feel like we have extra time now. And my day job has been keeping me busy. I just need one good day and I should be able to finish updating the remaining files. I hope to be able to make time for that before the end of the year. Here's my list of tasks that still need to be completed.  
  
* Update v2/shared_mutex.hpp (if this implementation is still needed)  
* Update win32/basic_timed_mutex.hpp  
* Update win32/basic_recursive_mutex.hpp  
* Update win32/shared_mutex.hpp  
* Make 100 millisecond interval configurable

---

## Comment 63

> Username: viboes  
> Created_at: 2017-11-28 17:47:43 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-347605760  

Hi Austin. I understand. You did already a really good work.  
  
I have had also other things to do on my side and I have not addressed anything since a long time.  
  
Hopping I will had as you say, one day before the end of the year to rework on this PR.

---

## Comment 64

> Username: austin-beer  
> Created_at: 2018-02-15 22:56:06 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-366089901  

Hi Vicente,  
  
I just wanted to see if you're still waiting on anything from me before merging this PR back to develop? I believe I've finished all of the tasks that I needed to do, but if there's anything further you need from me please let me know!  
  
Austin

---

## Comment 65

> Username: viboes  
> Created_at: 2018-02-16 07:12:50 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-366160569  

Really sorry for the delay. I did it just now.

---

## Comment 66

> Username: austin-beer  
> Created_at: 2018-02-16 16:20:53 UTC  
> Url: https://github.com/boostorg/thread/pull/142#issuecomment-366282727  

Great, thanks!

---
