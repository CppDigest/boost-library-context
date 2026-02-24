# #80 - Is track_foreign multithread safe? [Closed]

> Username: nero-ruijie  
> Created at: 2025-04-07 09:33:06 UTC  
> Updated at: 2025-04-11 15:36:57 UTC  
> Closed at: 2025-04-11 15:36:56 UTC  
> Url: https://github.com/boostorg/signals2/issues/80  

From my understanding, the value returned by std::weak_ptr::lock can still be an empty shared_ptr (nullptr), even when expired() returns false. This leads to some confusion regarding the absence of operator bool or operator== in the foreign_void_weak_ptr class. Is it safe to use track_foreign in a multithreaded context?

---

## Comment 1

> Username: fmhess  
> Created at: 2025-04-11 15:36:56 UTC  
> Url: https://github.com/boostorg/signals2/issues/80#issuecomment-2797262295  

It should be thread safe.  If you think there is a problem, please provide example code demonstrating it.
