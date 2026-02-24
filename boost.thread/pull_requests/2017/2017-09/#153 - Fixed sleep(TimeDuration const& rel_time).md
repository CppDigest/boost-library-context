# #153 Fixed sleep(TimeDuration const& rel_time) [Merged]

> Username: austin-beer  
> Created at: 2017-09-19 21:10:33 UTC  
> Updated at: 2017-09-19 21:50:03 UTC  
> Merged at: 2017-09-19 21:45:46 UTC  
> Closed at: 2017-09-19 21:45:46 UTC  
> Url: https://github.com/boostorg/thread/pull/153  

Fixed sleep(TimeDuration const& rel_time) when internal clock is not monotonic but CLOCK_MONOTONIC is available.

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-09-19 21:11:18 UTC  
> Url: https://github.com/boostorg/thread/pull/153#issuecomment-330674788  

Here are the test results for this PR. What do you think of my approach?  
  
[linux_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1315856/linux_results_austin_no_monotonic.txt)  
[linux_results_austin_yes_monotonic.txt](https://github.com/boostorg/thread/files/1315857/linux_results_austin_yes_monotonic.txt)

---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-19 21:24:42 UTC  
> Url: https://github.com/boostorg/thread/pull/153#issuecomment-330678050  

Super. I've already forgotten that we have now timespec clocks :)

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-09-19 21:25:25 UTC  
> Url: https://github.com/boostorg/thread/pull/153#issuecomment-330678227  

Yep! I just remembered myself.

---
