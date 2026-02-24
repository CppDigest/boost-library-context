# #256 - cygwin: condition_variable::timed_wait can return false even if a proper notification occurs [Closed]

> Username: jeking3  
> Created at: 2018-11-24 15:23:37 UTC  
> Updated at: 2018-11-25 13:01:17 UTC  
> Closed at: 2018-11-25 07:51:40 UTC  
> Url: https://github.com/boostorg/thread/issues/256  

In condition_variable::timed_wait where a system_time is passed in (absolute time), there was a code change made in 2017 that modified the behavior when monotonic clocks are used.  Here is the code (https://github.com/boostorg/thread/blob/develop/include/boost/thread/pthread/condition_variable.hpp#L227):  
```  
            // The system time may jump while this function is waiting. To compensate for this and time  
            // out near the correct time, we could call do_wait_until() in a loop with a short timeout  
            // and recheck the time remaining each time through the loop. However, because we can't  
            // check the predicate each time do_wait_until() completes, this introduces the possibility  
            // of not exiting the function when a notification occurs, since do_wait_until() may report  
            // that it timed out even though a notification was received. The best this function can do  
            // is report correctly whether or not it reached the timeout time.  
            const detail::platform_duration d(ts - detail::real_platform_clock::now());  
            do_wait_until(m, detail::internal_platform_clock::now() + d);  
            return ts > detail::real_platform_clock::now();  
```  
  
I've always relied on `timed_wait` to return true if the condition was met, false if it was not (which meant a timeout occurred).  The way the code is written here, it would be possible to get notified (return true from do_wait_until) and then return false.  What if the clock adjusted right after the notification, for example, or it was just on the tail end of the wait limit?  The caller would miss a notification.  That would be a difficult one to track down as a consumer.  
  
The opposite can happen as well, what if the clock adjusts backwards following the pthread call and we return true, but a notification didn't happen?  In that case we would return true.  
  
The code here is trying to be too fancy for what it is intended to do.  There are excessive calls to now().   
 As for the time check - why is it insufficient to rely on the pthread_cond_wait result here?  The notes specified at: https://en.cppreference.com/w/cpp/thread/condition_variable/wait_until seem reasonable here (yes, I know that is not a standards document).  If the clock is adjusted during the call, no guarantees are made.  I don't see a need to exceed this behavior, but we are trying to, and it can lead to odd behavior.

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-24 16:05:24 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441377720  

Note that we can have spurious notifications and the time is not reached.  
If the time is not reached the function should return false. period.  
  
Anyway, timed_wait is deprecated since a long time. I don't think anyone is using this yet.

---

## Comment 2

> Username: viboes  
> Created at: 2018-11-24 16:06:09 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441377778  

Why odd behavior?

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-24 16:43:42 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441380143  

I'm still trying to track it down, but on cygwin specifically test_mutex.cpp:52 and test_mutex.cpp:91 are failing randomly.  So either something is waking it up prematurely, or pthread_cond_wait is not waiting the complete amount of time, thus causing ts to be less than now() (in pthread/condition_variable.hpp:timed_wait, system_time).  
  
As an experiment I changed the implementation to return the result from do_wait_until, since that will return false if pthread_cond_wait returns ETIMEDOUT, which is the desired behavior.  This fixes the issue seen in the builds and locally for me.  Relying on the result of the pthread_cond_wait call is simpler, and also proves nothing was waking it up.  It must then be that the amount of time spent waiting in cygwin for pthread_cond_wait isn't always enough to ensure ts >= now() ?  Still looking...

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-24 23:09:20 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441402603  

I modified the code slightly so I could set a breakpoint in the debugger:  
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
Yes, a clock adjustment can occur during the call to  
pthread_cond_timedwait.  I don't think that's happening here however.    
  
The granularity of the cygwin code implementing pthread_cond_timedwait appears to  
use microseconds, which doesn't account for the gap.     
  
test_mutex states that nothing is going to signal the condition variable  
so it should always return false.  I agree with that.  Unfortunately due to a   
bug somewhere (probably in cygwin), we have an issue.  Perhaps on cygwin we  
return the result of do_timed_wait?  I'd prefer to do that on all platforms.  
  
Thoughts?

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-25 02:02:09 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441409445  

I can't get pthread_cond_timedwait to exhibit the same behavior in a sample program on cygwin, using a CLOCK_REALTIME.  
```  
#include <assert.h>  
#include <errno.h>  
#include <pthread.h>  
#include <stdio.h>  
#include <time.h>  
  
int main(void)  
{  
    pthread_cond_t cv;  
    pthread_mutex_t m;  
    struct timespec ts_begin;  
    struct timespec ts_target;  
    struct timespec ts_end;  
  
    assert(!pthread_mutex_init(&m, NULL));  
    assert(!pthread_cond_init(&cv, NULL));  
    assert(!pthread_mutex_lock(&m));  
  
    assert(!clock_gettime(CLOCK_REALTIME, &ts_begin));  
    ts_target = ts_begin;  
    ts_target.tv_nsec += 10000000; // add 10ms  
    if (ts_target.tv_nsec >= 1000000000)  
    {  
        ++ts_target.tv_sec;  
        ts_target.tv_nsec -= 1000000000;  
    }  
  
    assert(ETIMEDOUT == pthread_cond_timedwait(&cv, &m, &ts_target));  
    assert(!clock_gettime(CLOCK_REALTIME, &ts_end));  
  
    int ok = ts_end.tv_sec > ts_begin.tv_sec ||  
            (ts_end.tv_sec == ts_begin.tv_sec && ts_end.tv_nsec >= ts_begin.tv_nsec);  
    printf(" begin: %10us %10un\n",  ts_begin.tv_sec,  ts_begin.tv_nsec);  
    printf("target: %10us %10un\n", ts_target.tv_sec, ts_target.tv_nsec);  
    printf("   end: %10us %10un\n",    ts_end.tv_sec,    ts_end.tv_nsec);  
    printf("    ok: %s\n", ok ? "true" : "false");  
    return !ok; // unix error code 0 means ok  
}  
```  
  
Leads me to think there's some sort of rounding or math error in the internal/real/mono clock code in thread, but we'd be seeing the issue on other platforms too.  Will keep digging.

---

## Comment 6

> Username: viboes  
> Created at: 2018-11-25 07:29:07 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441421271  

I'm wondering if A. Williams the original author supported Cygwin platform using posix threads.  
Would you mind to contact him?

---

## Comment 7

> Username: viboes  
> Created at: 2018-11-25 07:45:31 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441421923  

I don't remember, can we say that cygwin has a monotonic clock?

---

## Comment 8

> Username: viboes  
> Created at: 2018-11-25 07:51:40 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441422158  

The documentation says:  
"std::cv_status::timeout if the absolute timeout specified by timeout_time was reached, std::cv_status::no_timeout overwise."  
  
This is what the function does.  
  
I've set the issue to invalid and closed it as the title is wrong.  
  
Please create a specific issue for the failing tests on cygwin.

---

## Comment 9

> Username: jeking3  
> Created at: 2018-11-25 13:01:17 UTC  
> Url: https://github.com/boostorg/thread/issues/256#issuecomment-441438863  

Fair enough.  Thanks.
