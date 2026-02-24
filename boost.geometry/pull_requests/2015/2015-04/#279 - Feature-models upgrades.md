# #279 Feature/models upgrades [Merged]

> Username: awulkiew  
> Created at: 2015-04-09 15:12:57 UTC  
> Updated at: 2015-04-22 20:01:24 UTC  
> Merged at: 2015-04-12 02:55:00 UTC  
> Closed at: 2015-04-12 02:55:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/279  

This PR adds some C++11 upgrades for geometries models:  
- support for std::initializer_list,  
- defaulted ctor for non-complex geometries (point, box, segment),  
  
compile-time checks:  
- segment's conept check Point  
- point's DimensionCount check using MPL_ASSERT_MSG  
  
and docs updates:  
- missing descriptions of ctors,  
- consistent description of ctors of non-complex geometries.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-04-09 16:02:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/279#issuecomment-91274681  

The last commit simplifies the `model::point` example. Since the user should prefer `bg::get` and `bg::set` there is no need to mention member functions. Actually it may be harmful for the user.  
  
The next step would be to add the examples for other geometries. In particular showing how the unified initialization syntax is supported in complex geometries models like `model::polygon`. As you probably remember, it looks similar to WKT:  
  
```  
// clockwise, closed polygon containing an exterior and interior ring  
polygon_t, poly{{{0,0},{0,5},{5,5},{5,0},{0,0}},{{1,1},{4,1},{4,4},{1,4},{1,1}}};  
```  
  
I'll wait with docs upgrades in case you had something against the changes proposed so far.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-04-09 19:17:09 UTC  
> Updated_at: 2015-04-09 19:49:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/279#discussion_r28093326  

This sample explains the class point.cpp, not the point concept. Therefore it was written as it was. The note is I think clear enough: prefer use bg::set. But it is good to have an example of the methods of the class itself too.  
Like point_xy which would give the usage of .x() and .y() (only there is no example there...)

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-04-09 19:21:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/279#issuecomment-91332997  

I'm OK with the changes and new examples for initializer lists, as long as is clearly indicated that it is C++11.  
As noted, I prefer keeping the point.cpp example as it was... Otherwise, the constructor should also go (prefer geometry::make), etc. But maybe it is good to mention (also in the example) that this example is specific for this class and not for the generic way of working.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2015-04-09 20:04:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/279#issuecomment-91342630  

I restored the example using get/set members for one of the coordinates. I also added an example for box, with C++11 feature. Is it clear enough?  
  
For further docs updates I'll create a separate PR to not obscure more important changes of this PR which are the C++11 features.

---
