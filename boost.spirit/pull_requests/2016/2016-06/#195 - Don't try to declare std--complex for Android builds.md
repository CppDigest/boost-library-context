# #195 Don't try to declare std::complex for Android builds [Merged]

> Username: KayEss  
> Created at: 2016-06-29 04:07:06 UTC  
> Updated at: 2016-06-29 10:39:51 UTC  
> Merged at: 2016-06-29 10:39:50 UTC  
> Closed at: 2016-06-29 10:39:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/195  

This breaks for clang as the NDK namespaces are oddly set up.  
  
The old version works for gcc builds on Android, but clang is now the default compiler with gcc deprecated.  
  
Isn't the declaration undefined behaviour anyway?

---

## Comment 1

> Username: djowel  
> Created_at: 2016-06-29 05:24:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/195#issuecomment-229259567  

Doh. That is indeed undefined behavior! Please update the PR fix by removing it entirely.

---

## Comment 2

> Username: djowel  
> Created_at: 2016-06-29 10:39:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/195#issuecomment-229320889  

Thanks!

---
