# #196 Fix final time jump issues [Merged]

> Username: austin-beer  
> Created at: 2017-12-20 20:34:42 UTC  
> Updated at: 2017-12-21 21:56:22 UTC  
> Merged at: 2017-12-21 17:41:01 UTC  
> Closed at: 2017-12-21 17:41:01 UTC  
> Url: https://github.com/boostorg/thread/pull/196  

The following implementations have been fixed:  
  
- include/boost/thread/win32/basic_timed_mutex.hpp  
- include/boost/thread/win32/basic_recursive_mutex.hpp  
- include/boost/thread/win32/shared_mutex.hpp  
- include/boost/thread/v2/shared_mutex.hpp

---

## Comment 1

> Username: viboes  
> Created_at: 2017-12-21 17:41:42 UTC  
> Url: https://github.com/boostorg/thread/pull/196#issuecomment-353412477  

even if there are multiple issues with macos, I've merged it as you are unable to fix them.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-12-21 17:42:42 UTC  
> Url: https://github.com/boostorg/thread/pull/196#issuecomment-353412686  

I would like you to do a PR with the test you have. maybe a complete folder for these test would be the best.

---

## Comment 3

> Username: austin-beer  
> Created_at: 2017-12-21 18:42:44 UTC  
> Url: https://github.com/boostorg/thread/pull/196#issuecomment-353426145  

It looks like all of the MAC OS test failures are due to not timing out when we should. My guess is there's an issue with how we're calculating or using the time on MAC OS.  
  
I'll work on a PR to merge my test code into a folder in the Boost.Thread library. I probably won't get to it until after the holiday break, though.

---

## Comment 4

> Username: viboes  
> Created_at: 2017-12-21 21:56:22 UTC  
> Url: https://github.com/boostorg/thread/pull/196#issuecomment-353467516  

I'll check my changes on the timespec for MacOs (but the test were ok after my change) and are ok in my MacOs :(  
  
No problem. I will have a holiday break also.  
  
BTW, thanks again.

---
