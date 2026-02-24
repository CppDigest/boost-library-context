# #344 Makes async_connect parametrizable and adjusts logs [Closed]

> Username: anarthal  
> Created at: 2025-10-29 19:39:52 UTC  
> Updated at: 2025-11-01 12:32:04 UTC  
> Closed at: 2025-10-30 12:29:42 UTC  
> Url: https://github.com/boostorg/redis/pull/344  

Makes success logs in async_connect use logger::level::debug  
Adds connect status logs in async_run

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-29 19:41:05 UTC  
> Url: https://github.com/boostorg/redis/pull/344#issuecomment-3463543683  

This is part of #269, and it's part of my Sentinel implementation (#237). The idea is untying `async_connect` from `config`, so we can pass any arbitrary server address.

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-10-30 12:29:42 UTC  
> Url: https://github.com/boostorg/redis/pull/344#issuecomment-3467758627  

I'm finally doing this as part of the Sentinel PR.

---
