# #348 Remove GCC 4.x, Clang 3.5 and C++03 [Merged]

> Username: jeremy-murphy  
> Created at: 2023-09-04 03:40:23 UTC  
> Updated at: 2023-09-12 00:47:46 UTC  
> Merged at: 2023-09-11 00:52:09 UTC  
> Closed at: 2023-09-11 00:52:09 UTC  
> Url: https://github.com/boostorg/graph/pull/348  

Remove testing of '98 and '03 language standards now that they are officially deprecated in Boost.  
  
Also removed the oldest GCC (4.x) and Clang (3.5) versions, somewhat arbitrarily. At some point I'll try to find a consensus on how many years we should remain backward compatible with compilers for.

---

## Comment 1

> Username: jeremy-murphy  
> Created_at: 2023-09-11 00:51:51 UTC  
> Url: https://github.com/boostorg/graph/pull/348#issuecomment-1713000264  

@jzmaddock , I'm going to merge this now because it's kind of an urgent fix, but if you think I made a mistake removing the old GCC and Clang, let me know.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2023-09-11 08:00:29 UTC  
> Url: https://github.com/boostorg/graph/pull/348#issuecomment-1713367201  

No, time to move on!  
  
Should probably be updated for more recent releases too....

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-09-12 00:47:46 UTC  
> Url: https://github.com/boostorg/graph/pull/348#issuecomment-1714803526  

> Should probably be updated for more recent releases too....  
  
Yeah, I'll do that next. Just needed to get the build green this time.

---
