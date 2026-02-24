# #494 update stdlib feature [Merged]

> Username: grisumbras  
> Created at: 2019-10-24 13:29:42 UTC  
> Updated at: 2021-10-02 21:13:45 UTC  
> Merged at: 2019-10-29 10:35:26 UTC  
> Closed at: 2019-10-29 10:35:26 UTC  
> Url: https://github.com/boostorg/build/pull/494  

Adds gnu11 (libstdc++ with new ABI) and libc++ values to stdlib feature.  
The value gnu was already in use by sun toolset, so this commit sets it as  
libstdc++ with old ABI. Adds support for that feature to gcc, clang-linux and  
clang-darwin toolsets. Refactors sun toolset to use stdlib feature via toolset  
flags.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2019-10-28 15:03:45 UTC  
> Url: https://github.com/boostorg/build/pull/494#issuecomment-546988904  

Can you fix the conflicts so I can see if it passes the tests?

---

## Comment 2

> Username: grisumbras  
> Created_at: 2019-10-28 15:05:37 UTC  
> Url: https://github.com/boostorg/build/pull/494#issuecomment-546989869  

Sure, I will do it this evening.

---
