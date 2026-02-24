# #43 Silence `-Wuninitialized` warnings in GCC 12 [Closed]

> Username: jngrad  
> Created at: 2022-05-30 14:18:43 UTC  
> Updated at: 2022-05-30 18:02:06 UTC  
> Closed at: 2022-05-30 17:55:24 UTC  
> Url: https://github.com/boostorg/function/pull/43  

Follow-up to #38 for GCC version 12. Fixes #42.

---

## Comment 1

> Username: pdimov  
> Created_at: 2022-05-30 16:02:54 UTC  
> Url: https://github.com/boostorg/function/pull/43#issuecomment-1141309551  

Are we sure we no longer need to disable `-Wmaybe-uninitialized` here?

---

## Comment 2

> Username: jngrad  
> Created_at: 2022-05-30 17:16:41 UTC  
> Url: https://github.com/boostorg/function/pull/43#issuecomment-1141360963  

> Are we sure we no longer need to disable `-Wmaybe-uninitialized` here?  
  
I couldn't reproduce the issue with `-Wno-uninitialized -Wmaybe-uninitialized`, but I guess it doesn't hurt to silence both on GCC 12.

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-05-30 17:55:24 UTC  
> Url: https://github.com/boostorg/function/pull/43#issuecomment-1141382301  

The conflict is because I already merged the previous version.

---
