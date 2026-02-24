# #109 Removes payload rotation from request. [Merged]

> Username: mzimbres  
> Created at: 2023-06-04 14:21:36 UTC  
> Updated at: 2023-06-04 14:50:22 UTC  
> Merged at: 2023-06-04 14:50:12 UTC  
> Closed at: 2023-06-04 14:50:12 UTC  
> Url: https://github.com/boostorg/redis/pull/109  

The user can simply call HELLO before other commands. Altering the order of requests makes it impossible to declare responses.

---
