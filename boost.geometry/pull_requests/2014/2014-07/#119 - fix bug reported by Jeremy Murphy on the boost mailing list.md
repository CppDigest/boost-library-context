# #119 [algorithms][get_turn_info] fix bug reported by Jeremy Murphy on the boost mailing list [Merged]

> Username: mkaravel  
> Created at: 2014-07-30 08:28:36 UTC  
> Updated at: 2014-08-01 20:46:50 UTC  
> Merged at: 2014-07-30 08:46:30 UTC  
> Closed at: 2014-07-30 08:46:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/119  

In `collinear_opposite` the `set_tp` method was assuming that all point types passed to it are the same; this  
is not the case: there are two different point types; the problem appeared when using `point_xy` in `rtree` and running intersects queries; in this case `get_turn_info` was instantiated with two different point types, the one used by the user and the one used by the `rtree` to store boxes, which were different.

---
