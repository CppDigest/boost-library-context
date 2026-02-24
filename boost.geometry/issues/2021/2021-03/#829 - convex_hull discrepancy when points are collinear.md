# #829 - convex_hull discrepancy when points are collinear. [Closed]

> Username: ayushgupta138  
> Created at: 2021-03-19 17:54:51 UTC  
> Updated at: 2021-09-22 22:11:14 UTC  
> Closed at: 2021-09-22 22:11:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/829  

When `boost::geometry::convex_hull()` function is called on collinear points, the returned polygon/linestring is incorrect and is inconsistent.  
for example, the following code snippet,  
` typedef boost::geometry::model::d2::point_xy<double> point2d;`  
` using bg=boost::geometry;`  
 `bg:model::polygon<point2d> poly1;`  
 `bg::model::multi_point<point2d> p1;`  
`bg::read_wkt("MULTIPOINT(2 1,3 2,4 3,7 6)", p1);`  
`bg::convex_hull(p1,poly1);`  
`cout<<bg::wkt(poly1)<<"\n";`  
outputs,  
`POLYGON(2 1,7 6,2 1,2 1)`  
similar output is obtained when a linestring is used to store the hull.  
This behaviour is caused by   
https://github.com/boostorg/geometry/blob/8b6929193db73f55b75563db1fdd4e86ded3d6fa/include/boost/geometry/algorithms/detail/convex_hull/graham_andrew.hpp#L322  
  
What is the use of the above mentioned line that I am missing?

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-03-19 21:20:43 UTC  
> Updated at: 2021-03-19 21:25:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/829#issuecomment-803140529  

I guess it's an edge case. It's impossible to create a convex hull of collinear points that is a valid polygon [1]. It's also the case for two points, a single segment, any number of collinear segments, a single point, any number of equal points, etc. The resulting polygon will have no interior.  
  
The requirement of at least 4 points for closed polygon is correct. Open polygon should have at least 3 distinct points and closed polygon should have 4 with the last equal to the first one. So the result is correct (i.e. this is by design) but the polygon is invalid.  
  
However I'm not saying that this behavior should stay. We could do something about it. I have several ideas. We could:  
1. throw an exception that it's not possible to calculate the areal convex hull.  
2. generate empty geometry if it is not possible to calculate the convex hull.  
3. change/implement version of the algorithm for linear output and use it to hold linear result (collinear points)  
4. change/implement version of the algorithm for pointlike output and use it to hold pointlike result (single point or equal points)  
5. allow taking several kinds of geometries as output, e.g. geometry collection (not ready for that yet) or tuple of various multi-geometries (like we currently have in set operations, though it's undocummented, this should be simple).  
  
2-4 would force the user to call the algorithm multiple times, once for each output geometry.  
  
1-3(4?) would probably be a breaking change. Some users may currently rely on that in some cases an invalid polygon is returned by the algorithm so they can anylize it (check validity or area) and transform the output themselves. And they could still choose to do this even if it was possible to call the algorithm several times for different kinds of output.  
  
[1] For the definition of polygon validity see: https://www.ogc.org/standards/sfa

---

## Comment 2

> Username: ayushgupta138  
> Created at: 2021-03-20 07:49:49 UTC  
> Updated at: 2021-03-20 13:34:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/829#issuecomment-803267768  

I think making the 2nd argument of `convex_hull()` to be mandatorily be a `GEOMETRY COLECTION` would be a good idea. This would increase the simplicity and the flexibility of the algorithm implementation. For example in case of collinearity, we could return a `LINESTRING` in the `GEOMETRY COLLECTION` geometry instead of a `POLYGON`. But since there is currently no support for `GEOMETRY COLLECTION` in Boost.Geometry, 3. and 4. seems a good option. @awulkiew what do you think?
