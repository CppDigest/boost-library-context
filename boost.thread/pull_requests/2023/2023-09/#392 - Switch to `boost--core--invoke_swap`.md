# #392 Switch to `boost::core::invoke_swap` [Closed]

> Username: Lastique  
> Created at: 2023-09-02 21:02:43 UTC  
> Updated at: 2023-09-11 18:16:15 UTC  
> Closed at: 2023-09-11 18:13:28 UTC  
> Url: https://github.com/boostorg/thread/pull/392  

`boost::swap` is deprecated and will be removed. Use `boost::core::invoke_swap` as a replacement.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-09-11 16:19:35 UTC  
> Url: https://github.com/boostorg/thread/pull/392#issuecomment-1714203043  

The changes to swap_T_pass.cpp don't compile, so I merged https://github.com/boostorg/thread/pull/393 instead.

---
