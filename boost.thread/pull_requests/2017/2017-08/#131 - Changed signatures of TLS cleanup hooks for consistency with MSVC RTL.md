# #131 Changed signatures of TLS cleanup hooks for consistency with MSVC RTL. [Merged]

> Username: Lastique  
> Created at: 2017-08-27 16:24:10 UTC  
> Updated at: 2017-08-27 19:01:45 UTC  
> Merged at: 2017-08-27 18:57:04 UTC  
> Closed at: 2017-08-27 18:57:04 UTC  
> Url: https://github.com/boostorg/thread/pull/131  

This should fix startup of executables statically linked with Boost.Thread  
because of undefined return value of the thread/process startup hooks.  
  
Should fix https://svn.boost.org/trac10/ticket/12730.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-08-27 18:56:57 UTC  
> Url: https://github.com/boostorg/thread/pull/131#issuecomment-325217274  

Thanks a lot.

---
