# #145 Fixing race condition on static init [Closed]

> Username: biocomp  
> Created at: 2015-08-05 17:42:45 UTC  
> Updated at: 2015-08-05 17:52:50 UTC  
> Closed at: 2015-08-05 17:52:27 UTC  
> Url: https://github.com/boostorg/spirit/pull/145  

Static variables depend on each other and when static init is not thread-safe, this leads to race condition and failures (on, say, Visual Studio 2013 update 4).

---

## Comment 1

> Username: biocomp  
> Created_at: 2015-08-05 17:52:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/145#issuecomment-128090431  

Closed pull request to main because I was asked to create one for develop

---
