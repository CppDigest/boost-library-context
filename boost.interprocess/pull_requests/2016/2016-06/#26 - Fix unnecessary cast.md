# #26 Fix unnecessary cast [Closed]

> Username: tabe  
> Created at: 2016-06-07 03:42:29 UTC  
> Updated at: 2016-11-13 20:01:33 UTC  
> Closed at: 2016-11-13 20:01:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/26  

as g++ warns with -Wcast-qual.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-11-13 20:01:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/26#issuecomment-260208580  

Thanks for the patch. Unfortunately, in some Unixes shmat receives a "void *" pointer so a portable code must uncast it first. Code was rewritten to avoid warnings and achieve portable behaviour in commit:  
  
https://github.com/boostorg/interprocess/commit/622bc616d3a4f0546765bd4622503aa3e2420b30

---
