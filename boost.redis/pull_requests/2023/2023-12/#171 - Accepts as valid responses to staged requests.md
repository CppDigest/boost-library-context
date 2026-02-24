# #171 Accepts as valid responses to staged requests. [Merged]

> Username: mzimbres  
> Created at: 2023-12-16 22:42:07 UTC  
> Updated at: 2023-12-16 23:17:23 UTC  
> Merged at: 2023-12-16 23:17:11 UTC  
> Closed at: 2023-12-16 23:17:11 UTC  
> Url: https://github.com/boostorg/redis/pull/171  

Before these changes the request had to be marked as written in order to interpret incoming responses as belonging to that request. On fast networks however, like on localhost and under load the responses might arrive before the write operation completed.

---
