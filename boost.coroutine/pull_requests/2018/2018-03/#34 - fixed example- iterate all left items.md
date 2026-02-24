# #34 fixed example: iterate all left items [Merged]

> Username: lorond  
> Created at: 2018-03-23 12:40:48 UTC  
> Updated at: 2018-03-23 12:49:19 UTC  
> Merged at: 2018-03-23 12:49:15 UTC  
> Closed at: 2018-03-23 12:49:15 UTC  
> Url: https://github.com/boostorg/coroutine/pull/34  

Items was not iterated when several items left in another source while current source has ended.  
  
a : 1 5 6 10   
b : 2 4 7 8 9 **13 15 17**   
c : 1 2 4 5 6 7 8 9 10 **13 13 13**   
  
fixed to   
  
a : 1 5 6 10   
b : 2 4 7 8 9 **13 15 17**   
c : 1 2 4 5 6 7 8 9 10 **13 15 17**

---

## Comment 1

> Username: olk  
> Created_at: 2018-03-23 12:49:19 UTC  
> Url: https://github.com/boostorg/coroutine/pull/34#issuecomment-375654670  

ty

---
