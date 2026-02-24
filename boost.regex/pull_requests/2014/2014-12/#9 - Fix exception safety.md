# #9 Fix exception safety. [Closed]

> Username: ya1gaurav  
> Created at: 2014-12-31 05:19:02 UTC  
> Updated at: 2014-12-31 19:33:48 UTC  
> Closed at: 2014-12-31 19:33:48 UTC  
> Url: https://github.com/boostorg/regex/pull/9  

At line no:251 node is allocated. In case of exception throw or assert , it should be deleted.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-12-31 19:33:48 UTC  
> Url: https://github.com/boostorg/regex/pull/9#issuecomment-68464217  

No not deleted, just unlocked and marked as unused, see https://github.com/boostorg/regex/commit/39aa10d82d6187d5cc33859b0bf8b96e5c7569e7  
  
Note however that this component of Boost.Regex is deprecated.

---
