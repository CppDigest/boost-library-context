# #93 mem_fun_ptr store ptr actor by value  [Merged]

> Username: Kojoley  
> Created at: 2020-03-21 20:27:40 UTC  
> Updated at: 2020-03-21 22:53:51 UTC  
> Merged at: 2020-03-21 22:49:42 UTC  
> Closed at: 2020-03-21 22:49:42 UTC  
> Url: https://github.com/boostorg/phoenix/pull/93  

Fixes hard to avoid use-after-scope bugs (tests already have them, fixes #73).  
Storing ptr actor also strips one level of indirection and is an optimization.  
  
Also had to do some CI maintenance work, added sanitized jobs and ccache.

---

## Comment 1

> Username: djowel  
> Created_at: 2020-03-21 22:49:32 UTC  
> Url: https://github.com/boostorg/phoenix/pull/93#issuecomment-602115501  

> Fixes hard to avoid use-after-scope bugs (tests already have them, fixes #73).  
> Storing ptr actor also strips one level of indirection and is an optimization.  
>   
> Also had to do some CI maintenance work, added sanitized jobs and ccache.  
  
Wonderful!

---
