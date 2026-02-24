# #196 fix heap-use-after-free condition when cancelling spawned task, close… [Open]

> Username: Pele44  
> Created at: 2024-08-12 13:09:46 UTC  
> Updated at: 2025-06-24 18:29:30 UTC  
> Url: https://github.com/boostorg/cobalt/pull/196  

This change corrects the error reported in issue 194. However, I do not have much experience with cobalt interiors, so it is worth having the author take a look at it.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-10-29 15:45:50 UTC  
> Url: https://github.com/boostorg/cobalt/pull/196#issuecomment-2444665375  

This should be fixed in https://github.com/boostorg/cobalt/commit/66c1196d2871ab29d104b8dc84ae379cf5410100 I think.   
  
Can you check?

---
