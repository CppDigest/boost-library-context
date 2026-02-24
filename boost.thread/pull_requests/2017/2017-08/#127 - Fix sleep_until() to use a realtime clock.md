# #127 Fix sleep_until() to use a realtime clock [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-08-02 22:34:36 UTC  
> Updated at: 2017-08-03 21:00:40 UTC  
> Merged at: 2017-08-03 21:00:24 UTC  
> Closed at: 2017-08-03 21:00:24 UTC  
> Url: https://github.com/boostorg/thread/pull/127  

boost::this_thread::no_interruption_point::hidden::sleep_until() takes an absolute time as a parameter and converts it to a duration for the length of time to sleep.  But the current time that the absolute time is compared against came from timespec_now(), which, on Linux at least, uses CLOCK_MONOTONIC, which "represents monotonic time since some unspecified starting point."  Since timespec_now() may have a different starting point than the time that was passed to sleep_until(), that can result in sleep_until() sleeping for an *extremely* long time, causing the program to appear to hang.  
  
Change sleep_until() to get the current time from timespec_now_realtime(), which uses CLOCK_REALTIME, which has the same epoch as the time that is passed to sleep_until().

---

## Comment 1

> Username: viboes  
> Created_at: 2017-08-03 21:00:40 UTC  
> Url: https://github.com/boostorg/thread/pull/127#issuecomment-320088853  

Thanks for this PR.

---
