# #380 [view] Use view_storage in views [Merged]

> Username: ricejasonf  
> Created at: 2018-01-03 00:23:51 UTC  
> Updated at: 2018-01-03 04:26:55 UTC  
> Merged at: 2018-01-03 04:26:55 UTC  
> Closed at: 2018-01-03 04:26:55 UTC  
> Url: https://github.com/boostorg/hana/pull/380  

- All views should use `view_storage` so there can't be  
    dangling references to view objects when composing views.  
  - Adds use of `view_storage` in `identity_view` and `single_view`

---
