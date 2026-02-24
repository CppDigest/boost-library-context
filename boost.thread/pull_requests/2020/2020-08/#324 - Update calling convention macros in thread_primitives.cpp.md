# #324 Update calling convention macros in thread_primitives.cpp [Merged]

> Username: mbs-c  
> Created at: 2020-08-11 08:13:33 UTC  
> Updated at: 2022-07-05 18:48:34 UTC  
> Merged at: 2022-07-05 18:48:33 UTC  
> Closed at: 2022-07-05 18:48:33 UTC  
> Url: https://github.com/boostorg/thread/pull/324  

The switch to the new calling convention macros in Boost.WinAPI in PR #214 seems to have been incomplete. I could only build Boost.Thread in our project by using the new calling convention macros in thread_primitives.cpp as well.

---
