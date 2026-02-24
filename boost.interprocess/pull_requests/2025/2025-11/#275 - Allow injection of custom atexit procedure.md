# #275 Allow injection of custom atexit procedure [Merged]

> Username: MiguelCompany  
> Created at: 2025-11-11 11:45:58 UTC  
> Updated at: 2025-11-11 22:18:14 UTC  
> Merged at: 2025-11-11 22:17:37 UTC  
> Closed at: 2025-11-11 22:17:37 UTC  
> Url: https://github.com/boostorg/interprocess/pull/275  

I came to a situation where a singleton in my application (let's call it `MySingleton`) with references to some boost interprocess objects was destroyed after the boost interprocess singleton.  
  
The destructor of `MySingleton` was then calling the boost interprocess API to free the relevant resources, and random crashes occurred.  
  
I've been using the following changes for a while now and decided to contribute them back.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2025-11-11 22:18:14 UTC  
> Url: https://github.com/boostorg/interprocess/pull/275#issuecomment-3518928782  

Thanks for the patch!

---
