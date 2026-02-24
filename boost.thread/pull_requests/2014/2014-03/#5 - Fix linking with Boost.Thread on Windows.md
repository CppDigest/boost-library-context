# #5 Fix linking with Boost.Thread on Windows. [Closed]

> Username: Lastique  
> Created at: 2014-03-03 07:42:20 UTC  
> Updated at: 2014-07-02 01:28:44 UTC  
> Closed at: 2014-03-06 21:12:27 UTC  
> Url: https://github.com/boostorg/thread/pull/5  

Other libraries are failing to link with Boost.Thread on Windows because GetTickCount64 is not marked with __declspec(dllimport).

---

## Comment 1

> Username: viboes  
> Created_at: 2014-03-04 22:45:51 UTC  
> Url: https://github.com/boostorg/thread/pull/5#issuecomment-36688561  

Already fixed. Thanks anyway.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-03-05 05:20:02 UTC  
> Url: https://github.com/boostorg/thread/pull/5#issuecomment-36711123  

The function implementation in gettickcount64.cpp is still not marked. Are you sure you don't need this change?

---

## Comment 3

> Username: viboes  
> Created_at: 2014-03-06 19:03:10 UTC  
> Url: https://github.com/boostorg/thread/pull/5#issuecomment-36923666  

IIUC we need only it on the declaration. See function e.g. steady_clock::now

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-03-06 21:12:27 UTC  
> Url: https://github.com/boostorg/thread/pull/5#issuecomment-36937541  

I remember having problems with some older MSVC which didn't export a symbol unless both declaration and definition were marked. That's why I always mark both. But I'm not insisting, I'll close the request if there is no longer a problem.

---
