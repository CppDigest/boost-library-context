# #4 Fix for utc_to_local year 2038 problem [Merged]

> Username: MarcelRaad  
> Created at: 2014-01-20 12:27:02 UTC  
> Updated at: 2014-06-17 10:19:22 UTC  
> Merged at: 2014-02-07 03:32:28 UTC  
> Closed at: 2014-02-07 03:32:28 UTC  
> Url: https://github.com/boostorg/date_time/pull/4  

Even if std::time_t is 64-bit, multiplying the 32-bit value dd.days() by 86400 results in a negative value for dates after 2038. This results in c_time::localtime(&t2, &tms) throwing an exception.  
  
Adding casts to std::time_t fixes this problem.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-02-07 03:32:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/4#issuecomment-34401257  

Tested several gcc versions on Linux, all good, thanks again!

---
