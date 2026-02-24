# #346 Spherical segments intersection strategy [Merged]

> Username: awulkiew  
> Created at: 2016-04-12 11:40:50 UTC  
> Updated at: 2016-06-21 15:57:51 UTC  
> Merged at: 2016-06-21 15:56:15 UTC  
> Closed at: 2016-06-21 15:56:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/346  

This PR adds `relate_spherical_segments` strategy and makes it the default intersection strategy for spherical_equatorial and geographic coordinate systems.  
- `relate_spherical_segments` intersection strategy is added.  
- `strategy::intersection::services::default_strategy` is added and specialized for various coordinate systems.  
- `strategy_intersection` defining intersection and side strategies is renamed to `intersection_strategies`, it now uses the default strategies so the intersection strategy is now compatible with side strategy for non-cartesian coordinate systems.  
- a new directory `formulas` is added and currently contains cartesian3d<->spherical_equatorial2d conversions. An alternative would be moving them to `util` however this way (having `formulas` directory) allows us to move other formulas like ssf, andoyer, vincenty, thomas, etc.  
- `cross_product` is moved from extensions with slightly changed interface  
  
I think the strategy itself is ready for merging, now I plan to add tests for various algorithms in non-cartesian coordinate systems and fix possible errors (in strategy and algorithms) so it could be a part of this PR or added later.  
  
UPDATE:  
  
`relate_spherical_segments` strategy uses the same approach as spherical side strategy, i.e. it represents the spherical segments in 3D cartesian, calculates normals of planes containing great circles and intersection point(s) of circles if necessary.  
  
Currently segment ratios' numerator and denominator values are corresponding to dot_products of two points (so cosines of angle between points) altered accordingly for the opposite direction. So a range [-1, 1] is mapped to [0, 2] and then negated and normalized if necessary which results in range (-2, 2] representing (-pi, pi]. Interpolation of ratio is not linear like it is in the case of cartesian intersection. If this caused problems `acos` could be calculated.  
  
I added tests for `get_turns()`, `relate()` and `relation()` for geometries combinations using intersection strategy (L/L, L/A, A/A) and did some tweaking as a result of testing.  
  
UPDATE 2:  
  
Because `equals()` for Areal geometries use `colect_vectors()` instead of `relate()` for optimization purposes I was forced to alter it. Now `collected_vector` is dispatched by coordinate system. For non-cartesian CSes currently spherical system is assumed which is consistent with the default intersection and side strategies for these systems. The internal implementation is also consistent with these strategies i.e. the direction of a segment is represented as vector normal to the plane containing the endpoints.  
  
I added tests for all relational operations.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-06-03 20:39:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/346#issuecomment-223688758  

I think the PR is ready for a review.  
  
I'm considering one more thing though. In `segments_intersection_points` for collinear segments the original Points stored in input geometry are assigned into the output with `detail::assign_point_from_index`. In cartesian intersection strategy for crossing segments new Point is created and only first two coordinates are set, which theoretically leaves the other coordinates and members uninitialized. In spherical intersection strategy added by this PR when intersection point is equal to an endpoint, this endpoint is assigned, so similar to collinear case. With that said it's unclear for me if that is correct behavior, e.g. if 2 endpoints meet (from 2 different input segments) at this intersection point, which one should be picked, considering that other members could be different? For segments crossing somewhere in the middle new Point is created and first two coordinates are set, so this is the same as in cartesian version. My question is, should we think about a consistent, well-defined behavior for newly created Points and those assigned from one of the input geometries?  
  
Another question (actually related to the above). Should we think about coordinates in higher dimensions? Currently only two first dimensions are taken into account. This means that e.g. two Polygons on different heights are still detected as intersecting. Should we still calculate the intersection considering geometries as 2D and therefore always horizontal or should we take the other coordinates into account? If the latter was preferred we'd have two options, Polygons could always be required to be horizontally aligned or not, i.e. lie on arbitrary rotated plane (or surface for non-cartesian CSes).

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2016-06-08 10:10:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/346#issuecomment-224546894  

Thanks for the PR! Iit is quite large, I understand that (it is a large addition to our library in terms of functionality too). I did do a review but did not review every single line...  
  
I'm in favour of merging this anyway.  
  
Can you give a short description of the completeness? Segment-intersections are added, obviously, but what needs still to be done after this, to reach for example polygon/polygon intersections? Or is that already done too? (Because I see related code)

---

## Comment 3

> Username: awulkiew  
> Created_at: 2016-06-08 10:51:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/346#issuecomment-224555091  

Polygon/Polygon operations use Segment/Segment strategy. All algorithms using Segment/Segment intersection strategy should work. However I haven't added tests for setops so it's possible that something is missing. Though there was never the tests for setops in spherical CS so this "something" would be missing from the beginning.  
  
Another important thing is the performance regression for spherical and geographic geometries. The cartesian intersection and side strategies were used for non-cartesian geometries. The users may be surprised that suddenly their algorithms are slower.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2016-06-08 20:32:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/346#issuecomment-224718335  

Thanks Adam, clear. I think it is logical that the performance is different for a real spherical implementation, so I think that the users who used cartesian in the past for spherical (there will not be many) will understand. But we should get an impression of the difference and, if necessary / possible, try to improve.

---

## Comment 5

> Username: vissarion  
> Created_at: 2016-06-21 14:14:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/346#issuecomment-227452902  

Looks OK to me too, thanks!

---
