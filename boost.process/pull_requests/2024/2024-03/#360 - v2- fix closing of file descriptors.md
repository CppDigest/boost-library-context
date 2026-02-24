# #360 v2: fix closing of file descriptors [Merged]

> Username: ceggers-arri  
> Created at: 2024-03-27 12:19:48 UTC  
> Updated at: 2024-03-28 13:57:13 UTC  
> Merged at: 2024-03-28 13:57:13 UTC  
> Closed at: 2024-03-28 13:57:13 UTC  
> Url: https://github.com/boostorg/process/pull/360  

Fix off-by-one error. Currently, no handles are actually closed.

---
