# #84 Fix compiler error with release builds on VS2015 [Merged]

> Username: nre-ableton  
> Created at: 2016-12-22 12:50:24 UTC  
> Updated at: 2017-12-08 10:03:56 UTC  
> Merged at: 2017-05-26 21:34:07 UTC  
> Closed at: 2017-05-26 21:34:07 UTC  
> Url: https://github.com/boostorg/graph/pull/84  

When using MSVC compiler optimization, using `param_not_found()` causes compiler error [C4172][1]: returning address of local variable or temporary  
  
  
[1]: https://msdn.microsoft.com/en-us/library/d0ws2xs1.aspx

---

## Comment 1

> Username: jcageman  
> Created_at: 2017-12-08 09:28:00 UTC  
> Url: https://github.com/boostorg/graph/pull/84#issuecomment-350216309  

I got the exact compile error fixed here using 1.65.1. Why is this not in 1.65.1?

---

## Comment 2

> Username: nre-ableton  
> Created_at: 2017-12-08 09:57:48 UTC  
> Url: https://github.com/boostorg/graph/pull/84#issuecomment-350222810  

@jcageman According to the [changelog](http://www.boost.org/users/history/version_1_65_1.html), 1.65.1 does not contain an updated version of the graph module. Hopefully it will be shipped in the next release.

---

## Comment 3

> Username: jcageman  
> Created_at: 2017-12-08 10:01:26 UTC  
> Url: https://github.com/boostorg/graph/pull/84#issuecomment-350223609  

Thanks for the quick reply. This is necessary to get boost compiling with VS2015/2017 so i would argue its a pretty important fix (maybe just on its own without other graph changes). Could you at least ensure it gets the right priority for next release? For now i will fix this in our local version.

---

## Comment 4

> Username: nre-ableton  
> Created_at: 2017-12-08 10:02:31 UTC  
> Url: https://github.com/boostorg/graph/pull/84#issuecomment-350223850  

@jcageman Sorry, I'm not affiliated with the Boost project, I just made the patch. 😉

---

## Comment 5

> Username: jcageman  
> Created_at: 2017-12-08 10:03:56 UTC  
> Url: https://github.com/boostorg/graph/pull/84#issuecomment-350224137  

Oke, thanks for the patch at least!

---
