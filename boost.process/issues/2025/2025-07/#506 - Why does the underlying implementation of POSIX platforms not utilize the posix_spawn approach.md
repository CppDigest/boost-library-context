# #506 - Why does the underlying implementation of POSIX platforms not utilize the posix_spawn approach? [Open]

> Username: Holux0206  
> Created at: 2025-07-07 09:58:36 UTC  
> Updated at: 2025-07-07 11:03:41 UTC  
> Url: https://github.com/boostorg/process/issues/506  

Why does the underlying implementation of POSIX platforms not utilize the posix_spawn approach? Is there a plan to switch to posix_spawn in the future?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-07-07 10:29:34 UTC  
> Url: https://github.com/boostorg/process/issues/506#issuecomment-3044372675  

Because it's too limited when it comes to extending functionality, i.e. running code after forking.   
  
I don't see why I'd need to switch either, you can just write your own launcher or construct a process from an existing `pid`.
