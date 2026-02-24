# #188 Port to `boost/core/snprintf.hpp` [Closed]

> Username: Lastique  
> Created at: 2022-12-11 22:36:56 UTC  
> Updated at: 2022-12-15 12:43:44 UTC  
> Closed at: 2022-12-13 18:51:06 UTC  
> Url: https://github.com/boostorg/regex/pull/188  

This replaces `snprintf`/`swprintf` workarounds with the more portable definitions in Boost.Core. This fixes compilation errors on MinGW32 introduced in a142dfecda7644e58382f4b6c527f37309af03f4.  
  
Also converted a few tabs to spaces.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-12-13 18:51:06 UTC  
> Url: https://github.com/boostorg/regex/pull/188#issuecomment-1349514843  

I've handled this slightly differently in https://github.com/boostorg/regex/pull/189 in order to maintain standalone mode, let me know if I've missed anything.

---
