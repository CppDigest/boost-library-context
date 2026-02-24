# #400 Use pointer_traits.to_address instead of addressof(*p) [Merged]

> Username: glenfe  
> Created at: 2017-05-29 18:06:54 UTC  
> Updated at: 2017-05-31 11:31:10 UTC  
> Merged at: 2017-05-31 11:31:10 UTC  
> Closed at: 2017-05-31 11:31:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/400  

addressof(*p) before construct() is not well-defined, since p does not alias storage that has an object constructed in it yet.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-05-31 11:30:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/400#issuecomment-305163344  

Thanks!

---
