# #39 Rename xtime to xtime_ to avoid conflict with MSVC's ::xtime [Merged]

> Username: pdimov  
> Created at: 2019-05-08 13:32:30 UTC  
> Updated at: 2019-05-08 14:49:21 UTC  
> Merged at: 2019-05-08 14:49:21 UTC  
> Closed at: 2019-05-08 14:49:21 UTC  
> Url: https://github.com/boostorg/chrono/pull/39  

As pointed out in https://github.com/boostorg/system/issues/44, including `<mutex>` defines `struct xtime` in the global namespace.

---
