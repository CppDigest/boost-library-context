# #130 Simplifications in the parser [Merged]

> Username: mzimbres  
> Created at: 2023-07-30 08:58:16 UTC  
> Updated at: 2023-07-30 09:45:04 UTC  
> Merged at: 2023-07-30 09:44:58 UTC  
> Closed at: 2023-07-30 09:44:58 UTC  
> Url: https://github.com/boostorg/redis/pull/130  

This PR has simplified the parser massively and made it simpler to do RESP3 sans-io. Calls to async_read_until and async_read have been replaced with async_read_some. The net result is that a lot of redundant reschedules and buffer rotations have been avoided. In local benchmarks the performance increased from 140k messages/s to 390k/s.

---
