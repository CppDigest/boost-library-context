# #540 Make side calculation for linear endpoints clearer. [Merged]

> Username: awulkiew  
> Created at: 2018-12-15 00:57:25 UTC  
> Updated at: 2018-12-15 19:10:25 UTC  
> Merged at: 2018-12-15 18:09:20 UTC  
> Closed at: 2018-12-15 18:09:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/540  

It seems that not all sides has to be calculated so this PR also adds an optimization for L/A case.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2018-12-15 09:42:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/540#pullrequestreview-185348888  

Thanks! Agree with merging this.

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 418 |                     // TODO: find out why side is used with respect to non-segments
 419 |                     // (that is: range2.at(0) to range1.at(0), denoted as x)
```

> Username: barendgehrels  
> Created_at: 2018-12-15 09:34:16 UTC  
> Updated_at: 2018-12-15 18:08:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/540#discussion_r241943945  

This comment can be removed now

> Username: awulkiew  
> Created_at: 2018-12-15 18:08:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/540#discussion_r241956577  

Done.


📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_la.hpp

```diff
 824 |+                 {
 825 |+                     side_pi_y = sides.apply(range_q.at(0), range_q.at(1), range_p.at(0)); // pi wrt q1
 826 |+                     side_pi_x = side_pi_y; // pi wrt q1
```

> Username: barendgehrels  
> Created_at: 2018-12-15 09:41:47 UTC  
> Updated_at: 2018-12-15 18:08:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/540#discussion_r241944200  

Great, so all sides are real sides now, a point w.r.t. an existing segment.  
Much better and better documented and named.  
I'm glad you worked on this last bit, you know and understand this code much better than I do.


---
