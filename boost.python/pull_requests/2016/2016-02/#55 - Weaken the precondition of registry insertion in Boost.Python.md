# #55 Weaken the precondition of registry insertion in Boost.Python. [Closed]

> Username: camio  
> Created at: 2016-02-10 00:31:41 UTC  
> Updated at: 2018-04-09 01:14:58 UTC  
> Closed at: 2018-04-09 01:14:58 UTC  
> Url: https://github.com/boostorg/python/pull/55  

Instead of prohibiting multiple registrations of the same type, we only  
prohibit (but don't check) that multiple registrations of the same type  
don't conflict.  
  
This enables the same 'class_' call to be used in different scopes as long  
as the (semantically) same converter is used.

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2016-04-21 13:50:32 UTC  
> Url: https://github.com/boostorg/python/pull/55#issuecomment-212928863  

I believe I just answered this request on the mailing list: https://mail.python.org/pipermail/cplusplus-sig/2016-April/017395.html  
  
Having multiple registrations for C++ <-> Python type conversions isn't possible right now. There has been discussion on tying this to individual modules, rather than having this an application-global scope, but implementing this is a lot of work.  
However, in this case I don't even see the need to support multiple registrations, as the problem can be solved more elegantly with a different approach anyhow.

---
