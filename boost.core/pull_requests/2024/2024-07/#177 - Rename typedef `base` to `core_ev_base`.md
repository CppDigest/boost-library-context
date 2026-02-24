# #177 Rename typedef `base` to `core_ev_base` [Merged]

> Username: ashtum  
> Created at: 2024-07-13 09:17:28 UTC  
> Updated at: 2024-07-13 10:21:01 UTC  
> Merged at: 2024-07-13 10:21:01 UTC  
> Closed at: 2024-07-13 10:21:01 UTC  
> Url: https://github.com/boostorg/core/pull/177  

Using typedef `base` poses a high risk of name conflict, given that `empty_value` is inherited in user types.

---
