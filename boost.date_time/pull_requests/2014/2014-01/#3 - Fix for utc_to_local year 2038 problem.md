# #3 Fix for utc_to_local year 2038 problem [Closed]

> Username: MarcelRaad  
> Created at: 2014-01-20 12:20:56 UTC  
> Updated at: 2014-06-17 10:19:22 UTC  
> Closed at: 2014-01-20 12:22:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/3  

Even if std::time_t is 64-bit, multiplying the 32-bit value dd.days() by 86400 result in a negative value for dates after 2038. This results in c_time::localtime(&t2, &tms) throwing an exception.  
  
Adding casts to std::time_t fixes this problem.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2014-01-20 12:23:33 UTC  
> Url: https://github.com/boostorg/date_time/pull/3#issuecomment-32755247  

Sorry, I accidently opened this pull request for master instead of develop.

---
