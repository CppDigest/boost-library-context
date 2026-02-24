# #153 152 enable reading server pushes in batches [Merged]

> Username: mzimbres  
> Created at: 2023-09-10 11:08:31 UTC  
> Updated at: 2023-09-10 20:28:33 UTC  
> Merged at: 2023-09-10 20:28:28 UTC  
> Closed at: 2023-09-10 20:28:28 UTC  
> Url: https://github.com/boostorg/redis/pull/153  

The performance improvement since the boost review is huge. The benchmark I have been using, which multiplex 1000 connections is processing at a rate of 3M messages/second, after the boost review it was only 250k messages/second, a 12-fold improvement.

---
