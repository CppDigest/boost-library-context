# #195 Fix/cart intersect [Merged]

> Username: awulkiew  
> Created at: 2015-01-19 19:23:24 UTC  
> Updated at: 2015-02-13 19:51:28 UTC  
> Merged at: 2015-01-19 23:11:38 UTC  
> Closed at: 2015-01-19 23:11:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/195  

This PR fixes the assertion failure for specific cases when a collinear segments' endpoints are not detected as intersecting in `cart_intersect -> direction policy`, but should be detected as such if e.g. `equals()` function was used. It's because only the ratios are compared. This may lead to some inconsistencies, e.g. the generation of only one intersection point for a segment detected as collinear (should be detected as collinear-touch) and an assertion failure in `get_turn_info()` algorithm analysing the `intersection_result`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-01-19 21:06:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/195#issuecomment-70560676  

That is quick! Thanks. I looked at the changes and I'm OK with merging.

---
