# #340 Add range::back_inserter() and use it in algorithms when needed. [Merged]

> Username: awulkiew  
> Created at: 2016-01-23 13:04:07 UTC  
> Updated at: 2017-03-20 14:29:39 UTC  
> Merged at: 2016-01-25 18:46:08 UTC  
> Closed at: 2016-01-25 18:46:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/340  

This PR adds `range::back_insert_iterator` calling using Boost.Geometry version of push_back() specialized in traits and replaces the `std::back_inserter()` with `range::back_inserter()` in places where points are added to geometries.

---
