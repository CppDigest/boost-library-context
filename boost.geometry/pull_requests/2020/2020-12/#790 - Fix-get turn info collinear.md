# #790 Fix/get turn info collinear [Merged]

> Username: barendgehrels  
> Created at: 2020-12-30 13:32:09 UTC  
> Updated at: 2021-01-13 08:36:20 UTC  
> Merged at: 2021-01-13 08:36:14 UTC  
> Closed at: 2021-01-13 08:36:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/790  

This PR handles, in rare cases, collinear as if it was equal, because the arriving segment arrives at the intersection point in the same way as it is in equal (but at an undetectable distance).  
  
In terms of statistics this reduces the error count from 444 to 241 (in recursive_polygons_buffer)  
  
There are several tests added, some of them are fixed by this test, some remain to be fixed but it's more convenient to have them in this PR already.  
  
There is also a bit cleanup in get_turn_info in the same way I added the new code.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2021-01-11 13:48:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/790#pullrequestreview-565355765  

Looks ok to me, thanks!

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 946 |+         }
 947 |+ 
 948 |+         auto const location = distance_measure(info.intersections[1],
```

> Username: vissarion  
> Created_at: 2021-01-11 13:33:26 UTC  
> Updated_at: 2021-01-13 08:34:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/790#discussion_r555048539  

regarding the name `location` maybe `distance_from_location` or something is more relevant since this is not a location but some kind of distance.

> Username: barendgehrels  
> Created_at: 2021-01-13 08:25:46 UTC  
> Updated_at: 2021-01-13 08:34:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/790#discussion_r556339858  

Agreed, I don't know why I took `location`. I will change it, and merge. Thanks for your review.

> Username: barendgehrels  
> Created_at: 2021-01-13 08:35:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/790#discussion_r556345763  

In other places I used `dm` for this purpose so I use that now here too.


---
