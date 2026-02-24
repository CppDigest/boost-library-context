# #66 Workaround clang not exporting symbols when hidden visibility is in effect [Closed]

> Username: Lastique  
> Created at: 2018-08-20 11:54:20 UTC  
> Updated at: 2018-08-25 19:33:16 UTC  
> Closed at: 2018-08-25 17:07:23 UTC  
> Url: https://github.com/boostorg/regex/pull/66  

This temporary workaround forces global (a.k.a. "default") visibility by default  
when building the library or tests with clang. A proper fix would involve  
figuring out what causes the compiler to not export the missing symbols  
and adjusting the code accordingly.  
  
This is related to https://github.com/boostorg/regex/issues/49 and https://github.com/boostorg/boost/pull/190.

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-08-20 11:55:43 UTC  
> Url: https://github.com/boostorg/regex/pull/66#issuecomment-414290709  

Note that if there is a proper fix for this problem then this PR is not needed. I'm not sure I will be able to dig into the code enough to provide a proper fix.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-08-25 17:07:23 UTC  
> Url: https://github.com/boostorg/regex/pull/66#issuecomment-415983315  

I've disabled external template instantiation in https://github.com/boostorg/regex/commit/40ecdc3f8b52cc6833f05b37610e916824ba88c7

---
