# #84 Add `mp_sliding_fold` and `mp_sliding_fold_q` [Merged]

> Username: k3DW  
> Created at: 2023-07-31 04:00:37 UTC  
> Updated at: 2023-11-27 10:36:36 UTC  
> Merged at: 2023-11-27 10:36:35 UTC  
> Closed at: 2023-11-27 10:36:35 UTC  
> Url: https://github.com/boostorg/mp11/pull/84  



---

## Comment 1

> Username: pdimov  
> Created_at: 2023-07-31 10:27:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/84#issuecomment-1658096386  

Looks good, but msvc-12.0 and msvc-14.0 have problems with it. I'll add a workaround and merge it, but probably after 1.83 ships.  
  
The clang-15 failure is a CI problem which I'll fix as well.

---
