# #182 - Feature Request: Add support for monotonic/steady_clock timeouts [Closed]

> Username: davidsj2  
> Created at: 2022-07-06 23:27:06 UTC  
> Updated at: 2024-07-05 08:14:25 UTC  
> Closed at: 2024-07-05 08:14:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/182  

Current synchronization objects use absolute time for timeouts, which are susceptible to unexpected behavior in the face of clock adjustments.  It would be nice to support relative timeouts using monotonic clock in interprocess condition variables and mutexes like is supported in POSIX and the C++ standard lib (generally), respectively.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-07-24 23:55:59 UTC  
> Url: https://github.com/boostorg/interprocess/issues/182#issuecomment-1193421669  

From Version 1.78 already supports relative timeouts (e.g. try_lock_for):  
  
https://www.boost.org/doc/libs/1_79_0/doc/html/boost/interprocess/interprocess_mutex.html#idm30792-bb  
  
Isn't that what you are requesting?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-07-05 08:14:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/182#issuecomment-2210413514  

Closing the issue as completed.
