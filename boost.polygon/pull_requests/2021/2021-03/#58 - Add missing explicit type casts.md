# #58 Add missing explicit type casts [Open]

> Username: mborn-adi  
> Created at: 2021-03-01 13:35:04 UTC  
> Updated at: 2021-03-01 13:35:45 UTC  
> Url: https://github.com/boostorg/polygon/pull/58  

Currently, the "coordinate_type" must be implicitly convertible to the "high_precision_type" type, because some explicit type casts are missing. With this change, that's no longer the case.  
This is very valuable for a custom "coordinate_type".

---
