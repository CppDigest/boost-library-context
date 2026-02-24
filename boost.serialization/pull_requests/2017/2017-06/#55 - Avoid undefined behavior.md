# #55 Avoid undefined behavior. [Closed]

> Username: jsonn  
> Created at: 2017-06-10 16:01:00 UTC  
> Updated at: 2017-11-08 17:26:02 UTC  
> Closed at: 2017-11-08 17:26:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/55  

Macros expanding to defined() expressions are undefined, move the conditional logic to outside the expression.

---

## Comment 1

> Username: jsonn  
> Created_at: 2017-06-10 16:20:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/55#issuecomment-307575071  

This is the same as ticket 12144 and 8120. Looking at the latter, the explicit MSVC can most likely be dropped.

---

## Comment 2

> Username: masterleinad  
> Created_at: 2017-11-01 11:29:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/55#issuecomment-341081590  

This is the same as  the recent commit 8d0b0db5deb54fd091e8467364b5ec293a9a8c6b.

---
