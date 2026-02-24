# #13 Enable the use of clock_gettime() on Nuxi CloudABI. [Merged]

> Username: EdSchouten  
> Created at: 2015-08-27 13:56:01 UTC  
> Updated at: 2015-08-27 14:20:25 UTC  
> Merged at: 2015-08-27 14:20:18 UTC  
> Closed at: 2015-08-27 14:20:18 UTC  
> Url: https://github.com/boostorg/log/pull/13  

Just like OpenBSD and HURD, CloudABI provides a clock_gettime() function  
without implementing timer_*(). It therefore cannot set _POSIX_TIMERS.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-08-27 14:20:25 UTC  
> Url: https://github.com/boostorg/log/pull/13#issuecomment-135450178  

Thanks.

---
