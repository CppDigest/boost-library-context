# #37 Fix trailing return type for iterator_interface comparison operators [Merged]

> Username: furkanusta  
> Created at: 2020-03-28 13:53:48 UTC  
> Updated at: 2020-04-05 20:12:42 UTC  
> Merged at: 2020-04-05 20:12:42 UTC  
> Closed at: 2020-04-05 20:12:42 UTC  
> Url: https://github.com/boostorg/stl_interfaces/pull/37  

Even though implementation works fine, the current one deduces non-bool types, which doesn't play nice with gcc10-ranges implementation.

---
