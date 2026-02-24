# #258 - [cygwin] condition_variable::timed_wait tests fail in test_mutex [Open]

> Username: jeking3  
> Created at: 2018-11-25 13:08:45 UTC  
> Updated at: 2018-11-29 20:25:03 UTC  
> Url: https://github.com/boostorg/thread/issues/258  

In test_mutex there are some simple tests that lock a mutex, then call timed_wait on a condition variable and the mutex.  Given no other code will notify the condition variable, the tests (test_mutex.cpp:52 and test_mutex.cpp:91) expect to result to be false.  On cygwin the result is sometimes true:  
  
https://ci.appveyor.com/project/jeking3/thread/builds/20524620/job/axu9rgvpm2h1djgm?fullLog=true#L1626  
  
I was able to reproduce this locally in the debugger by modifying timed_wait such that I could place a breakpoint on the failure:  
```  
Thread 17 "test_mutex" hit Breakpoint 1, boost::condition_variable_any::timed_wait<boost::detail::try_lock_wrapper<boost::recursive_mutex> > (this=0xffc4cb2c, m=..., abs_time=...)  
    at ../../../boost/thread/pthread/condition_variable.hpp:249  
249                     return result || true;  
(gdb) l  
244                 detail::real_platform_timepoint before = detail::real_platform_clock::now();  
245                 const detail::platform_duration d(ts - before);  
246                 bool result = do_wait_until(m, detail::internal_platform_clock::now() + d);  
247                 detail::real_platform_timepoint after = detail::real_platform_clock::now();  
248                 if (ts > after)  
249                     return result || true;  
250                 return false;  
251     #else  
252                 return do_wait_until(m, ts);  
253     #endif  
(gdb) p before  
$1 = {dur = {ts_val = {tv_sec = 1543097563, tv_nsec = 498393000}}}  
(gdb) p d  
$2 = {ts_val = {tv_sec = 0, tv_nsec = 598000000}}  
(gdb) p after  
$3 = {dur = {ts_val = {tv_sec = 1543097564, tv_nsec = 96388700}}}  
(gdb) p result  
$4 = false  
(gdb) p after - before  
[New Thread 27380.0x74a4]  
$5 = {ts_val = {tv_sec = 0, tv_nsec = 597995700}}  
(gdb) p ts  
$6 = {dur = {ts_val = {tv_sec = 1543097564, tv_nsec = 96393000}}}  
```  
The `after` time is 4300us before the end of `ts` which is the time point that should have passed.  This likely means there is a rounding error somewhere, either in the time handling of boost.thread or down in cygwin.

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-25 13:35:35 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441440989  

Please, could you try to see if this is xtime specific?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-25 13:35:47 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441441000  

Good news - I completed the test program to isolate the issue and I can prove it is a problem in cygwin:  
```  
Jim@pulsar /cygdrive/c/users/jim/desktop  
$ while "./timed_wait_short.exe"; do :; done  
 begin:     520424s  785831800n  
target:     520424s  835831800n  
   end:     520424s  836902100n  
    ok: true  
 begin:     520424s  860788400n  
target:     520424s  910788400n  
   end:     520424s  911174300n  
    ok: true  
 begin:     520424s  937973900n  
target:     520424s  987973900n  
   end:     520424s  987900300n  
    ok: false  
  
Jim@pulsar /cygdrive/c/users/jim/desktop  
$ while "./timed_wait_short.exe"; do :; done  
 begin:     520427s  138223600n  
target:     520427s  188223600n  
   end:     520427s  188227700n  
    ok: true  
 begin:     520427s  213095200n  
target:     520427s  263095200n  
   end:     520427s  263004000n  
    ok: false  
```  
  
Here's the source code:  
```  
$ cat timed_wait_short.c  
#include <assert.h>  
#include <errno.h>  
#include <pthread.h>  
#include <stdio.h>  
#include <time.h>  
  
int main(void)  
{  
    pthread_cond_t cv;  
    pthread_condattr_t attr;  
    pthread_mutex_t m;  
    struct timespec ts_begin;  
    struct timespec ts_target;  
    struct timespec ts_end;  
  
    assert(!pthread_condattr_init(&attr));  
    pthread_condattr_setclock(&attr, CLOCK_MONOTONIC);  
    assert(!pthread_mutex_init(&m, NULL));  
    assert(!pthread_cond_init(&cv, &attr));  
    assert(!pthread_mutex_lock(&m));  
  
    assert(!clock_gettime(CLOCK_MONOTONIC, &ts_begin));  
    ts_target = ts_begin;  
    ts_target.tv_nsec += 50000000; // add 50ms  
    if (ts_target.tv_nsec >= 1000000000)  
    {  
        ++ts_target.tv_sec;  
        ts_target.tv_nsec -= 1000000000;  
    }  
  
    assert(ETIMEDOUT == pthread_cond_timedwait(&cv, &m, &ts_target));  
    assert(!clock_gettime(CLOCK_MONOTONIC, &ts_end));  
  
    int ok = ts_end.tv_sec > ts_target.tv_sec ||  
            (ts_end.tv_sec == ts_target.tv_sec && ts_end.tv_nsec >= ts_target.tv_nsec);  
    printf(" begin: %10us %10un\n",  ts_begin.tv_sec,  ts_begin.tv_nsec);  
    printf("target: %10us %10un\n", ts_target.tv_sec, ts_target.tv_nsec);  
    printf("   end: %10us %10un\n",    ts_end.tv_sec,    ts_end.tv_nsec);  
    printf("    ok: %s\n", ok ? "true" : "false");  
    return !ok; // unix error code 0 means ok  
}  
```  
This is only an issue when you set the condition variable's clock to monotonic.  It does not happen with the realtime clock.  I'll submit another issue to the cygwin mailing list (is there really no bug database, and just a mailing list?).  As for the tests, we can disable these two tests that are failing under cygwin until the underlying platform is fixed.

---

## Comment 3

> Username: viboes  
> Created at: 2018-11-25 13:49:30 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441441783  

What about disabling the monotonic clock in cygwin?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-25 14:56:20 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441446358  

Looking through the sources for cygwin, it looks like all the other timed calls use the real clock, and there was a commit in Feb 2018 that changed the code:  
  
https://cygwin.com/git/gitweb.cgi?p=newlib-cygwin.git;a=blob;f=winsup/cygwin/thread.cc;h=0bddaf345d255ae39187458dc6d02b1b4c8087c1;hb=HEAD#l2546  
  
On line 2564 a calculation is made, but then later on at line 2574 (the case for the monotonic clock) it looks like the subtraction there is subject to rounding errors.  That's likely the root cause.  
  
We have a couple choices we could use to handle this:  
  
1. On cygwin only, for condition_variable::timed_wait, always return the result of do_timed_wait (which calls pthread_cond_timedwait, which is returning ETIMEDOUT).  This is a workaround for the underlying issue until they fix the issue.  
  
2. On cygwin only, disable these specific tests until they fix the issue.  
  
Checking the clock after the call to pthread_cond_timedwait still seems strange to me.  It would change the result from false to true if the clock was adjusted backwards, but it wouldn't change the result if the clock was adjusted forwards.  I still think it would be better to just return the result of pthread_cond_timedwait so the behavior of clock adjustment is handled by the platform.  That said, let me know what you are comfortable with.

---

## Comment 5

> Username: viboes  
> Created at: 2018-11-25 15:21:37 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441448164  

I'll not change the meaning of wait_until :(  
Again, what about disabling the monotonic clock in cygwin?

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-25 15:24:03 UTC  
> Updated at: 2018-11-25 15:39:09 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441448324  

Just for the condition variable handling, or across the board?  This case seems limited to just the condition variable with monotonic timer.  For now I'm modifying detail/config.hpp to not use the monotonic clock on cygwin.

---

## Comment 7

> Username: viboes  
> Created at: 2018-11-25 15:48:44 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441450130  

I don't catch the question?  
  
Go ahead for the change in the config file.

---

## Comment 8

> Username: viboes  
> Created at: 2018-11-26 06:40:29 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441534083  

Setting as enhancement it is a cygwin.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-11-26 20:58:24 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-441795526  

This issue was confirmed upstream, see:  
  
https://cygwin.com/ml/cygwin/2018-11/msg00187.html

---

## Comment 10

> Username: jeking3  
> Created at: 2018-11-29 20:25:03 UTC  
> Url: https://github.com/boostorg/thread/issues/258#issuecomment-442980610  

Upstream changes and snapshot available for testing:  
  
https://cygwin.com/snapshots/x86/cygwin-diffs-20181126-20181129
