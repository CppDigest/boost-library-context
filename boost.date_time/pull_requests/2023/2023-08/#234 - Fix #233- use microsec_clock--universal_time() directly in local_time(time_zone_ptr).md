# #234 Fix #233: use microsec_clock::universal_time() directly in local_time(time_zone_ptr) [Open]

> Username: boimart1  
> Created at: 2023-08-21 15:55:37 UTC  
> Updated at: 2023-08-21 15:56:44 UTC  
> Url: https://github.com/boostorg/date_time/pull/234  

Fixes #233.  
  
Calling `microsec_clock::universal_time()` directly avoids the issue where the second could change between the calls to `second_clock::local_time()` and `second_clock::universal_time()`.

---
