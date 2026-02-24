# #171 Fixing windows_shared_memory::get_name() [Merged]

> Username: yobeonline  
> Created at: 2022-02-28 11:59:26 UTC  
> Updated at: 2022-03-07 12:14:18 UTC  
> Merged at: 2022-03-07 12:14:18 UTC  
> Closed at: 2022-03-07 12:14:18 UTC  
> Url: https://github.com/boostorg/interprocess/pull/171  

Fixes issue #153.  
  
Restores the original behavior (pre 140b50efb3281fa3898f3a4cf939cfbda174718f).  
If `filename` is provided as a wide string, then `get_name()` will return an unspecified pointer.

---

## Comment 1

> Username: yobeonline  
> Created_at: 2022-03-07 12:03:48 UTC  
> Url: https://github.com/boostorg/interprocess/pull/171#issuecomment-1060615233  

I do not understand why those checked were cancelled. Does it mean there is something wrong with the changes shipped by this PR? If so, where should I look for to find out what?

---
