# #205 fix mingw build [Closed]

> Username: WSoptics  
> Created at: 2018-01-29 17:36:46 UTC  
> Updated at: 2018-01-29 19:52:55 UTC  
> Closed at: 2018-01-29 19:20:14 UTC  
> Url: https://github.com/boostorg/thread/pull/205  

The following change is necessary for me to cross-compile using `mingw`.

---

## Comment 1

> Username: viboes  
> Created_at: 2018-01-29 18:20:02 UTC  
> Url: https://github.com/boostorg/thread/pull/205#issuecomment-361337271  

Thanks for the PR.  
  
Please, could you show what was wrong on mingw build? This will help me to understand why do we need this change.

---

## Comment 2

> Username: WSoptics  
> Created_at: 2018-01-29 19:20:14 UTC  
> Url: https://github.com/boostorg/thread/pull/205#issuecomment-361355723  

Sorry, we don't need it.  It was caused by a dangling `winapi/wait.hpp` from another boost version in our repository and my stupidity.  Sorry for having wasted your time!

---

## Comment 3

> Username: viboes  
> Created_at: 2018-01-29 19:52:55 UTC  
> Url: https://github.com/boostorg/thread/pull/205#issuecomment-361365650  

Don't worry

---
