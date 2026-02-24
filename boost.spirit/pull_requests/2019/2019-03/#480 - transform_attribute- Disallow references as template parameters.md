# #480 transform_attribute: Disallow references as template parameters [Merged]

> Username: Kojoley  
> Created at: 2019-03-07 21:31:10 UTC  
> Updated at: 2019-03-08 02:07:27 UTC  
> Merged at: 2019-03-08 02:07:23 UTC  
> Closed at: 2019-03-08 02:07:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/480  

The special handling was used instead of removing references from the `make_attribute<T,U>::type`. Since `make_attribute` is gone the workaround is no longer needed.  
  
Closes #450

---
