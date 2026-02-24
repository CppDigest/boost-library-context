# #512 - centroid overflow for multipoint with integer coords [Open]

> Username: woodroof  
> Created at: 2018-10-02 04:49:28 UTC  
> Updated at: 2018-12-05 07:34:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/512  

centroid just sums all points and divide result on point count, but it should use "extended" type.  
Example:  
{{x = 989466522, y = 789595328}, {x = 989472652, y = 789598701}, {x = 989481089, y = 789595346}, {x = 989484158, y = 789593666}, {x = 989484932, y = 789586937}, {x = 989482640, y = 789580203}, {x = 989478814, y = 789574309}, {x = 989471911, y = 789576825}, {x = 989466522, y = 789595328}}  
Result: {x = 315354648, y = -1483637949}  
Expected result: {x = 989476582, y = 789588516}

---

## Comment 1

> Username: woodroof  
> Created at: 2018-10-14 16:08:41 UTC  
> Url: https://github.com/boostorg/geometry/issues/512#issuecomment-429638788  

Or at least library should provide a way to set ExtendedType in BOOST_GEOMETRY_REGISTER_POINT_2D and other macro.

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-12-03 19:48:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/512#issuecomment-443845565  

Hi, actually it is possible to specify the `CalculationType` used in `centroid()` algorithm. You can do this by passing a strategy explicitly however only the strategy for areal geometries like polygons supports this. There is another way however. I think if you defined another point type for the output point using bigger coordinate type or simply used `simply bg::model::point<>` with greater coordinate type than your "typical" geometry as temporary then this type would be used internally in `centroid()`.

---

## Comment 3

> Username: woodroof  
> Created at: 2018-12-05 07:34:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/512#issuecomment-444387498  

I can do as you suggest but it means additional convertion to another geometry type. It's actually worse than writing my own function for centroid calculation.  
I think this should be handled in library as user (as I am) may be surprised by unexpected behaviour.
