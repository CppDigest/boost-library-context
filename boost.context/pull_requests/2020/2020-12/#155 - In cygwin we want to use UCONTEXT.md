# #155 In cygwin we want to use UCONTEXT [Merged]

> Username: viric  
> Created at: 2020-12-16 19:12:53 UTC  
> Updated at: 2020-12-17 07:26:26 UTC  
> Merged at: 2020-12-17 07:26:22 UTC  
> Closed at: 2020-12-17 07:26:22 UTC  
> Url: https://github.com/boostorg/context/pull/155  

Crash otherwise.  
  
I haven't tried winfibers.  
  
I think this should fix https://github.com/boostorg/coroutine2/issues/34. Is this the kind of patch that could get in? Or how else?

---

## Comment 1

> Username: olk  
> Created_at: 2020-12-17 07:25:36 UTC  
> Url: https://github.com/boostorg/context/pull/155#issuecomment-747264096  

It is a workaround - better boost.context would be patched with correct assembler.

---

## Comment 2

> Username: olk  
> Created_at: 2020-12-17 07:26:26 UTC  
> Url: https://github.com/boostorg/context/pull/155#issuecomment-747264469  

ty

---
