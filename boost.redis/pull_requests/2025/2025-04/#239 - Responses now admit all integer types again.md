# #239 Responses now admit all integer types again [Merged]

> Username: anarthal  
> Created at: 2025-04-07 09:41:35 UTC  
> Updated at: 2025-06-06 11:05:34 UTC  
> Merged at: 2025-04-07 20:48:48 UTC  
> Closed at: 2025-04-07 20:48:48 UTC  
> Url: https://github.com/boostorg/redis/pull/239  

Responses can now be used again with signed char, unsigned char, short, unsigned short, unsigned int, long, and unsigned long.  
Added unit tests for conversions to integers, bool and double  
  
close #238

---

## Comment 1

> Username: mzimbres  
> Created_at: 2025-04-07 20:52:39 UTC  
> Url: https://github.com/boostorg/redis/pull/239#issuecomment-2784601515  

Thanks @anarthal  
  
@mclow Can I merge my `develop` into the `master` so it contains the fix for the release?

---
