# #253 Reconnection no longer causes the writer to perform busy waiting [Merged]

> Username: anarthal  
> Created at: 2025-05-18 17:19:03 UTC  
> Updated at: 2025-05-20 11:23:19 UTC  
> Merged at: 2025-05-19 14:46:56 UTC  
> Closed at: 2025-05-19 14:46:56 UTC  
> Url: https://github.com/boostorg/redis/pull/253  

Reconnection now uses a separate timer to wait  
  
close #252

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-05-18 17:19:30 UTC  
> Url: https://github.com/boostorg/redis/pull/253#issuecomment-2889106779  

Unfortunately, I don't see a way of adding a regression test for this. I've done a manual test.

---
