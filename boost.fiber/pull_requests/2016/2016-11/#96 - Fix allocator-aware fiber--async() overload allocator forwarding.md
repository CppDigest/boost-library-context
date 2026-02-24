# #96 Fix allocator-aware fiber::async() overload allocator forwarding. [Merged]

> Username: xaqq  
> Created at: 2016-11-15 15:27:12 UTC  
> Updated at: 2016-11-16 07:41:21 UTC  
> Merged at: 2016-11-16 07:41:21 UTC  
> Closed at: 2016-11-16 07:41:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/96  

I'm using a custom valgrind-aware allocator for creating fiber so that  
the valgrind log is clean.  
  
It worked well with fiber::fiber() but wasn't used in fiber::async().   
Unless there are reasons unknown to me, I believe this to be an oversight, and this  
commit fixes it.

---

## Comment 1

> Username: olk  
> Created_at: 2016-11-16 07:41:16 UTC  
> Url: https://github.com/boostorg/fiber/pull/96#issuecomment-260875804  

it was an oversight, thx

---
