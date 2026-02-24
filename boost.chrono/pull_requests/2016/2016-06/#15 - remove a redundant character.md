# #15 remove a redundant character [Merged]

> Username: rick68  
> Created at: 2016-06-19 20:07:45 UTC  
> Updated at: 2016-06-28 11:58:47 UTC  
> Merged at: 2016-06-28 11:58:45 UTC  
> Closed at: 2016-06-28 11:58:45 UTC  
> Url: https://github.com/boostorg/chrono/pull/15  

`boost::chrono::clock_string<boost::chrono::process_system_cpu_clock, char>::name()` returns `"process_systtem_clock"` that have a redundant character 't'.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-06-28 11:58:47 UTC  
> Url: https://github.com/boostorg/chrono/pull/15#issuecomment-229028377  

Thanks.

---
