# #687 Fix dangling reference in distance [Merged]

> Username: awulkiew  
> Created at: 2020-03-25 16:21:27 UTC  
> Updated at: 2020-03-26 14:12:12 UTC  
> Merged at: 2020-03-26 14:12:12 UTC  
> Closed at: 2020-03-26 14:12:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/687  



---

## Review 1 [Commented]

> Username: mloskot  
> Created_at: 2020-03-25 17:34:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/687#pullrequestreview-381380695  

📁 include/boost/geometry/algorithms/detail/distance/multipoint_to_geometry.hpp

```diff
   2 | 
   3 |- // Copyright (c) 2014, 2019, Oracle and/or its affiliates.
   3 |+ // Copyright (c) 2014, 2019, 2020 Oracle and/or its affiliates.
```

> Username: mloskot  
> Created_at: 2020-03-25 17:34:16 UTC  
> Updated_at: 2020-03-25 17:34:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/687#discussion_r398042027  

I'm curious: why not `2014-2020`?

> Username: awulkiew  
> Created_at: 2020-03-26 01:56:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/687#discussion_r398273597  

We have this policy about copyrights to put all of the dates in the message. Or at least I think we have. However I choose to drop it as soon as the line becomes too long. So I guess I may not wait until then and change it now if you like.

> Username: mloskot  
> Created_at: 2020-03-26 10:22:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/687#discussion_r398461304  

Yes, I've been interpreting the policy in such way that it considers `1,2,3` and  `1-3` as equivalents.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2020-03-26 10:14:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/687#pullrequestreview-381865574  

Thanks!

---
