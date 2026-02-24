# #234 Fixing compiling on VS2017 for ARM & ARM64 [Merged]

> Username: DjArt  
> Created at: 2018-09-14 19:21:23 UTC  
> Updated at: 2018-10-01 03:52:31 UTC  
> Merged at: 2018-10-01 03:49:16 UTC  
> Closed at: 2018-10-01 03:49:16 UTC  
> Url: https://github.com/boostorg/thread/pull/234  

https://github.com/boostorg/thread/issues/233

---

## Comment 1

> Username: viboes  
> Created_at: 2018-10-01 03:52:31 UTC  
> Url: https://github.com/boostorg/thread/pull/234#issuecomment-425782782  

I've finished by accepting the PR.  
  
The result of the function is used always by testing if it is != 0. So the cast should not make the things worst. I'd appreciate a PR for the static assert on the equality of the size of the types.

---
