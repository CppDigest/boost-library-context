# #285 Reduce dependency on geometry::disjoint algorithm [Merged]

> Username: mkaravel  
> Created at: 2015-04-27 12:11:21 UTC  
> Updated at: 2015-04-28 13:42:42 UTC  
> Merged at: 2015-04-27 14:55:43 UTC  
> Closed at: 2015-04-27 14:55:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/285  

Instead of calling `geometry::disjoint()` from within `wkt/write.hpp`, call `geometry::detail::disjoint::disjoint_point_point()` instead. This reduces the dependency on the `geometry::disjoint()` algorithm and avoids potential circular dependencies.

---
