# #58 Cease dependence on Thread [Merged]

> Username: Kojoley  
> Created at: 2021-06-12 16:40:23 UTC  
> Updated at: 2021-07-15 14:04:52 UTC  
> Merged at: 2021-07-15 14:03:58 UTC  
> Closed at: 2021-07-15 14:03:58 UTC  
> Url: https://github.com/boostorg/coroutine/pull/58  

On C++11 static local variables are initialized in thread-safe manner, but even on C++03 it should not be a problem because in our case variables are of trivial types, which means double initialization is not an issue, and they are initialized with the same value in every thread.

---

## Comment 1

> Username: olk  
> Created_at: 2021-07-15 14:04:26 UTC  
> Url: https://github.com/boostorg/coroutine/pull/58#issuecomment-880722236  

ty, but note that boost.coroutine is deprecated

---
