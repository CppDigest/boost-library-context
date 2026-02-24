# #2 Allow timer to be built with -fno-exceptions [Merged]

> Username: jzmaddock  
> Created at: 2015-09-06 08:55:30 UTC  
> Updated at: 2017-10-23 12:45:04 UTC  
> Merged at: 2017-10-23 12:44:38 UTC  
> Closed at: 2017-10-23 12:44:38 UTC  
> Url: https://github.com/boostorg/timer/pull/2  

This change allows the timer lib to be built with GCC and -fno-exceptions.  This is an issue for me while testing the Math lib with exceptions disabled (timer is an indirect dependency via Boost.Test).  In case you're wondering.... -fno-exceptions is a build requirement for a certain well known search engine company...

---

## Comment 1

> Username: Beman  
> Created_at: 2017-10-23 12:45:04 UTC  
> Url: https://github.com/boostorg/timer/pull/2#issuecomment-338647047  

Thanks,  
  
--Beman

---
