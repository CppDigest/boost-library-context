# #45 Fix drone CI tests [Closed]

> Username: sdarwin  
> Created at: 2021-09-13 20:37:06 UTC  
> Updated at: 2021-09-15 00:50:35 UTC  
> Closed at: 2021-09-15 00:49:00 UTC  
> Url: https://github.com/boostorg/url/pull/45  

- default OS Ubuntu 18.04  
- if standalone mode has been removed from URL, then also removing 'standalone' test.  
- the other cmake test is looking for all boost libraries & their CMakeLists.txt.  The solution is to build "in tree".  
- add missing libc++abi-dev

---

## Comment 1

> Username: sdarwin  
> Created_at: 2021-09-15 00:49:00 UTC  
> Updated_at: 2021-09-15 00:50:35 UTC  
> Url: https://github.com/boostorg/url/pull/45#issuecomment-919612075  

This was already merged.  Closing PR.

---
