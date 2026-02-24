# #15 Remove unread assignment [Merged]

> Username: 8W9aG  
> Created at: 2017-05-09 12:39:30 UTC  
> Updated at: 2017-09-08 19:42:15 UTC  
> Merged at: 2017-09-08 19:36:57 UTC  
> Closed at: 2017-09-08 19:36:57 UTC  
> Url: https://github.com/boostorg/system/pull/15  

The variable sz is not read after it is assigned to `0`. This shows up as an warning in clangs static analyzer.

---

## Comment 1

> Username: Beman  
> Created_at: 2017-09-08 19:37:40 UTC  
> Url: https://github.com/boostorg/system/pull/15#issuecomment-328195220  

Thanks,  
  
--Be,am

---

## Comment 2

> Username: Beman  
> Created_at: 2017-09-08 19:42:15 UTC  
> Url: https://github.com/boostorg/system/pull/15#issuecomment-328196120  

Grrr... I failed to notice that the pull request was against master instead of develop. Will fix. In the future please submit boost pull requests against develop.  
  
--Beman

---
