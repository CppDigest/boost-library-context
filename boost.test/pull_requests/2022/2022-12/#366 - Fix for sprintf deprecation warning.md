# #366 Fix for sprintf deprecation warning [Merged]

> Username: mborland  
> Created at: 2022-12-09 16:34:32 UTC  
> Updated at: 2023-02-02 16:27:34 UTC  
> Merged at: 2023-02-02 16:27:29 UTC  
> Closed at: 2023-02-02 16:27:29 UTC  
> Url: https://github.com/boostorg/test/pull/366  

Apple Clang 14 warns that `sprintf` is deprecated. Replace with the new `boost::core::snprintf` for compatibility with pre-c++11 code, and across toolchains.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2023-01-27 04:32:17 UTC  
> Url: https://github.com/boostorg/test/pull/366#issuecomment-1406007697  

@raffienficiaud : Might it be possible to give Matt write access to boost.test? He is now in the employ of boost.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2023-01-28 12:20:22 UTC  
> Url: https://github.com/boostorg/test/pull/366#issuecomment-1407387639  

Hi @NAThompson and @mborland ! I was approached by Boost Owners for this matter, there is discussion going on, but so far this is looking good! No problem on my side, I can grant you write access right now.  
We can also have a chat on the phone if you want.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2023-01-31 16:02:43 UTC  
> Url: https://github.com/boostorg/test/pull/366#issuecomment-1410648660  

@mborland : Is this ready to merge?

---

## Comment 4

> Username: mborland  
> Created_at: 2023-01-31 16:04:01 UTC  
> Url: https://github.com/boostorg/test/pull/366#issuecomment-1410650835  

I just got the CI system running so I was going to rebase onto it to ensure that it is good

---

## Comment 5

> Username: mborland  
> Created_at: 2023-02-02 16:27:18 UTC  
> Url: https://github.com/boostorg/test/pull/366#issuecomment-1414019464  

This is green with MSVC workaround so merging.

---
