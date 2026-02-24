# #28 Fix std:: forward declaration compile error on GCC with libc++ [Merged]

> Username: ilor  
> Created at: 2015-06-08 06:54:44 UTC  
> Updated at: 2015-06-11 07:51:46 UTC  
> Merged at: 2015-06-09 20:53:05 UTC  
> Closed at: 2015-06-09 20:53:05 UTC  
> Url: https://github.com/boostorg/container/pull/28  

The code should not assume that libc++ is only ever used with Clang.  
  
Fixes issue #11380, https://svn.boost.org/trac/boost/ticket/11380

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-06-09 08:55:47 UTC  
> Url: https://github.com/boostorg/container/pull/28#issuecomment-110281608  

Thanks! Merged to develop in:  
  
SHA-1: 8d04e0d259f73d8f4341d645df7a510c274c2845  
- Merge branch 'ilor-bug-11380' into develop

---
