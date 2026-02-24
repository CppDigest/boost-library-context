# #980 - sectionalize does not work for const points [Closed]

> Username: barendgehrels  
> Created at: 2022-02-23 12:28:09 UTC  
> Updated at: 2022-03-02 08:34:40 UTC  
> Closed at: 2022-03-02 08:34:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/980  

Sectionalize uses a box internally. That box uses the `point_type` of the passed geometry.  
  
If that geometry is `const` (no `set` methods in traits), it won't work.  
  
For example: `typedef typename point_type<Polygon>::type point_type;` [here](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/sections/sectionalize.hpp#L631)  
This is passed to `sectionalize_range` and a box is kept.  
  
There is more code where similar things happen, and which is why `boost::geometry::disjoint(a, b)` does not work for cases where `a` or `b` are correctly defined const geometries.  
  
I'm working at sectionalize (because it's the first step), which can be fixed relatively quickly by passing extra a writable point type.

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-02-23 12:33:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/980#issuecomment-1048736594  

Good catch. Another thing is that user-defined point may contain some other data that is not needed for space partitioning. This is why the R-tree defines its own box type:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L189  
We also have this utility:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/helper_geometry.hpp

---

## Comment 2

> Username: barendgehrels  
> Created at: 2022-02-23 13:05:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/980#issuecomment-1048761473  

> Good catch. Another thing is that user-defined point may contain some other data that is not needed for space partitioning. This is why the R-tree defines its own box type: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/index/rtree.hpp#L189 We also have this utility: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/helper_geometry.hpp  
  
Thanks! I'll probably use that helper geometry type.
