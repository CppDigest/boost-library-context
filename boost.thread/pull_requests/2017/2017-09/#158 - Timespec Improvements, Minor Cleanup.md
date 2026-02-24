# #158 Timespec Improvements, Minor Cleanup [Merged]

> Username: austin-beer  
> Created at: 2017-09-21 15:33:21 UTC  
> Updated at: 2017-09-21 16:51:05 UTC  
> Merged at: 2017-09-21 16:50:43 UTC  
> Closed at: 2017-09-21 16:50:43 UTC  
> Url: https://github.com/boostorg/thread/pull/158  

* Added a constructor to timespec_duration, real_timespec_timepoint, and mono_timespec_timepoint that takes an intmax_t representing the number of nanoseconds in the timespec.  
* Added a getNs() function to timespec_duration, real_timespec_timepoint, and mono_timespec_timepoint that returns an intmax_t representing the number of nanoseconds in the timespec.  
* Added a timespec_milliseconds() function that takes an integer representing milliseconds and returns a timespec_duration.  
* Removed some unnecessary BOOST_SYMBOL_VISIBLE declarations.  
* Removed the unnecessary d100 variable declarations.

---

## Review 1 [Commented]

> Username: viboes  
> Created_at: 2017-09-21 16:39:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/thread/pull/158#pullrequestreview-64347681  

📁 include/boost/thread/pthread/timespec.hpp

```diff
 114 |+     }
 115 |+ 
 116 |+     inline timespec_duration timespec_milliseconds(long const& ms)
```

> Username: viboes  
> Created_at: 2017-09-21 16:39:24 UTC  
> Updated_at: 2017-09-21 16:49:53 UTC  
> Url: https://github.com/boostorg/thread/pull/158#discussion_r140295694  

static?


---
