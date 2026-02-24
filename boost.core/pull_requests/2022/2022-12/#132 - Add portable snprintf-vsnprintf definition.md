# #132 Add portable snprintf/vsnprintf definition [Closed]

> Username: Lastique  
> Created at: 2022-12-06 18:22:58 UTC  
> Updated at: 2022-12-09 00:54:01 UTC  
> Closed at: 2022-12-09 00:53:54 UTC  
> Url: https://github.com/boostorg/core/pull/132  

This definitions is mostly a workaround for older MSVC versions that only provided non-portable `_snprintf` etc. that are not fully conforming to the standard `snprintf`. This implementation fixes its issues wrt. null termination and returned values in case of buffer overflows.  
  
On platforms that support the standard `snprintf`, the definitions in the header are equivalent to the standard functions.

---

## Review 1 [Approved]

> Username: pdimov  
> Created_at: 2022-12-07 23:38:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/core/pull/132#pullrequestreview-1209264562  

Looks good at a glance.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-12-09 00:15:57 UTC  
> Url: https://github.com/boostorg/core/pull/132#issuecomment-1343608745  

Are you waiting for me to merge this? It would be useful f.ex. for https://github.com/boostorg/lexical_cast/pull/56.

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-12-09 00:52:07 UTC  
> Url: https://github.com/boostorg/core/pull/132#issuecomment-1343690177  

I was going to merge it after the 1.81 release to avoid potential inconvenience in case if we need to merge develop to master for a pre-release bug fix. But I guess I can do it early.

---
