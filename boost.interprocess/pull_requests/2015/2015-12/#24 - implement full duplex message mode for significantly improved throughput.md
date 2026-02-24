# #24 implement full duplex message mode for significantly improved throughput [Closed]

> Username: dbeal  
> Created at: 2015-12-28 06:46:02 UTC  
> Updated at: 2024-07-06 20:57:17 UTC  
> Closed at: 2024-07-06 20:57:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/24  

Using a free stack of message pointers, dual mutexes, and moving the memcpy outside the lock, much higher message throughput can be achieved.

---

## Comment 1

> Username: dbeal  
> Created_at: 2017-06-14 19:20:14 UTC  
> Url: https://github.com/boostorg/interprocess/pull/24#issuecomment-308531905  

Hey guys have you had a chance to take a look at the code?  I'll be revisiting this soon, probably for more performance tests, and it would be great to get some preliminary feedback.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2024-07-06 20:57:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/24#issuecomment-2211966029  

Thanks for the patch. Closing old issues which haven't received interest from users in recent years.

---
