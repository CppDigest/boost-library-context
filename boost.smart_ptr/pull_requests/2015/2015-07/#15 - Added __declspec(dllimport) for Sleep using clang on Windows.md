# #15 Added __declspec(dllimport) for Sleep using clang on Windows. [Merged]

> Username: eldiener  
> Created at: 2015-07-21 19:48:20 UTC  
> Updated at: 2015-07-21 21:02:49 UTC  
> Merged at: 2015-07-21 21:02:49 UTC  
> Closed at: 2015-07-21 21:02:49 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/15  

This fixes a problem with the latest clang built from source. It also works fine with previous versions of clang on Windows. I will test this again once clang 3.7 is official but since it works with clang 3.4.1, clang 3.5.2, and clang 3.6.1 on Windows I doubt it will cause any problems once clang 3.7 on Windows is officially released. Needless to say this does not affect clang outside of Windows at all.

---

## Comment 1

> Username: pdimov  
> Created_at: 2015-07-21 20:20:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/15#issuecomment-123467023  

Is `BOOST_COMP_CLANG` defined when the compiler isn't Clang?

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-07-21 20:36:22 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/15#issuecomment-123470344  

According to the predef docs it is always defined but it is defined to 0 when the compiler isn't Clang.

---
