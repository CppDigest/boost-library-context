# #2 Fixed assembly support for PowerPC 32-bit XCOFF on AIX and added assembl... [Merged]

> Username: ismirlian  
> Created at: 2014-08-01 18:38:05 UTC  
> Updated at: 2014-08-01 18:56:45 UTC  
> Merged at: 2014-08-01 18:55:32 UTC  
> Closed at: 2014-08-01 18:55:32 UTC  
> Url: https://github.com/boostorg/context/pull/2  

...y support for PowerPC 64-bit XCOFF on AIX  
  
The assembly previously provided did not work for AIX 32-bit as the syntax is different, though I believe the semantics to be the same. With this fix although the tests still do not run correctly in submodules context and coroutine, they at least compile hopefully paving the way for debugging in the future.

---

## Comment 1

> Username: olk  
> Created_at: 2014-08-01 18:56:45 UTC  
> Url: https://github.com/boostorg/context/pull/2#issuecomment-50921812  

thx

---
