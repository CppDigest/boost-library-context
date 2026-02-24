# #87 Address unreachable code warning [Closed]

> Username: gieseanw  
> Created at: 2020-12-18 17:14:17 UTC  
> Updated at: 2020-12-18 23:03:10 UTC  
> Closed at: 2020-12-18 23:02:42 UTC  
> Url: https://github.com/boostorg/optional/pull/87  

In MSVC `optional<T&>::value()` emits a warning C4702: unreachable code because `throw_exception` is marked `BOOST_NORETURN`.  
Otherwise the ternary code might have been preferable.  
This change addresses the warning while preserving the functionality. It replicates `optional<T>::value()` for consistency.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2020-12-18 23:02:42 UTC  
> Updated_at: 2020-12-18 23:03:10 UTC  
> Url: https://github.com/boostorg/optional/pull/87#issuecomment-748361413  

Merged to develop.  
Thanks for the fix.

---
