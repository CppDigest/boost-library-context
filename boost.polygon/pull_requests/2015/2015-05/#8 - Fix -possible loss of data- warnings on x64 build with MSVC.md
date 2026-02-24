# #8 Fix "possible loss of data" warnings on x64 build with MSVC [Merged]

> Username: posila  
> Created at: 2015-05-18 15:54:50 UTC  
> Updated at: 2015-05-19 10:52:40 UTC  
> Merged at: 2015-05-19 10:52:40 UTC  
> Closed at: 2015-05-19 10:52:40 UTC  
> Url: https://github.com/boostorg/polygon/pull/8  

When building with 64 bit target MSVC 13 throws a few warnings about converting size_t to int   
"warning C4267: '=' : conversion from 'size_t' to 'int', possible loss of data"

---

## Comment 1

> Username: asydorchuk  
> Created_at: 2015-05-19 10:52:22 UTC  
> Url: https://github.com/boostorg/polygon/pull/8#issuecomment-103436667  

Hi @posila, thank you for the pull request!

---
