# #1409 feat: Implement is_valid algorithm for polyhedral surfaces [Merged]

> Username: vissarion  
> Created at: 2025-05-21 14:24:14 UTC  
> Updated at: 2025-09-03 12:19:00 UTC  
> Merged at: 2025-09-03 12:18:56 UTC  
> Closed at: 2025-09-03 12:18:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409  

This PR introduces support for the `is_valid` algorithm for polyhedral surface geometries. A polyhedral_surface is a 3D composite geometry formed by a collection of polygonal faces that meet at shared edges. The implementation follows the OGC Simple Features Specification and introduces structural and topological checks for polyhedral surfaces.  
  
The following properties should hold (following OGC standard):  
  
- Contiguity: All polygon patches must form a connected surface via shared boundary segments.  
- Shared Edges: Shared boundaries must be finite LineStrings used in at most two polygons.  
- Orientation: Shared boundaries must be traversed in opposite directions by adjacent polygons; global orientation must be consistent.  
  
Six new validity failure types has been introduced that reflect the description of the standard.   
  
Below there is a matrix with valid and invalid cases with visualizations. Those cases (and a few more) are included in the unit tests of this PR.   
  
| Name                                               | Geometry                                                                                                                                                                                                                                     | Figure |  
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|  
| valid_surface                                      | POLYHEDRALSURFACE(((0 0 0,0 1 0,1 1 0,1 0 0,0 0 0)),((0 0 0,0 0 1,0 1 1,0 1 0,0 0 0)),((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((1 1 1,0 1 1,0 0 1,1 0 1,1 1 1)),((1 1 1,1 0 1,1 0 0,1 1 0,1 1 1)),((1 1 1,1 1 0,0 1 0,0 1 1,1 1 1))) | ![Screenshot from 2025-05-21 16-52-54](https://github.com/user-attachments/assets/0ddb2629-bf4b-49ab-9992-ddfba7b1ee18) |  
| valid_surface_non_convex                          | POLYHEDRALSURFACE(((0 0 0,0 1 0,1 1 0,1 0 0,0 0 0)),((0 0 0,0 0 1,0 1 1,0 1 0,0 0 0)),((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((1 1 1,0 1 1,0 0 1,1 0 1,1 1 1)),((1 1 1,1 0 1,1 0 0,1 1 0,1 .5 .5,1 1 1)))               | ![Screenshot from 2025-05-21 16-53-30](https://github.com/user-attachments/assets/954adfe2-efb4-413e-9758-fd55c65c3d9e) |  
| valid_surface_holes                                | POLYHEDRALSURFACE(((0 0 0,0 1 0,1 1 0,1 0 0,0 0 0),(0.2 0.2 0,0.2 0.5 0,0.5 0.5 0,0.5 0.2 0,0.2 0.2 0)),((0 0 0,0 0 1,0 1 1,0 1 0,0 0 0)),((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((1 1 1,0 1 1,0 0 1,1 0 1,1 1 1)),((1 1 1,1 0 1,1 0 0,1 1 0,1 1 1)),((1 1 1,1 1 0,0 1 0,0 1 1,1 1 1))) | ![Screenshot from 2025-05-21 16-55-21](https://github.com/user-attachments/assets/72a00aa7-cf2f-4249-9805-21cbb63c0f47)  |  
| invalid_surface_non_planar_face                    | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 1,0 0 0)),((1 0 0,1 1 0,0 1 0,1 0 0)))                                                                                                                        | ![Screenshot from 2025-05-21 17-20-01](https://github.com/user-attachments/assets/f0ac921c-6bbb-4d00-b3fd-36267e1d702d)   |  
| invalid_surface_collinear_points                   | POLYHEDRALSURFACE(((1 0 0,1 1 0,0 1 0,1 0 0)),((0 0 0,1 0 0,2 0 0,0 0 0)))                                                                                                                              |  ![Screenshot from 2025-05-21 17-10-21](https://github.com/user-attachments/assets/0cbc3240-3088-44ba-b90d-bdfd87ce296c)     |  
| invalid_surface_few_points                         | POLYHEDRALSURFACE(((1 0 0,1 1 0,0 1 0,1 0 0)),((0 0 0,1 0 0,0 0 0)))                                                                                                                                    |  ![Screenshot from 2025-05-21 17-11-01](https://github.com/user-attachments/assets/160455b0-b0a6-4393-9ff8-1e9caf368e29)     |  
| invalid_surface_invalid_intersection_boundary_interior | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 0 1,0 0 0)),((0 1 0,0.5 -0.5 0.5,1 1 0,0 1 0)))                                                                                                                   | ![Screenshot from 2025-05-21 17-11-20](https://github.com/user-attachments/assets/7805e68c-d576-414d-90d7-999d1ff319f1)      |  
| invalid_surface_invalid_intersection_boundary_boundary | POLYHEDRALSURFACE(((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((0 1 0,0 -1 1,1 -1 1,1 1 0,0 1 0)))                                                                                                               | ![Screenshot from 2025-05-21 17-11-36](https://github.com/user-attachments/assets/abe12aea-b14c-480d-8a46-86f47abc980a)      |  
| invalid_surface_invalid_intersection_in_common_vertex | POLYHEDRALSURFACE(((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((0 1 0,1 0 1,1 1 0,0 1 0)))                                                                                                                      |  ![Screenshot from 2025-05-21 17-11-57](https://github.com/user-attachments/assets/d702ef12-04b6-4d83-8528-172449472010)     |  
| invalid_surface_invalid_intersection_partial_edge   | POLYHEDRALSURFACE(((0 0 0,1 0 0,1 0 1,0 0 1,0 0 0)),((0 1 0,0.5 0 1,1 0 1,1 1 0,0 1 0)))                                                                                                                | ![Screenshot from 2025-05-21 17-12-11](https://github.com/user-attachments/assets/a962160f-af17-40b1-85a2-357d514a97b1)      |  
| invalid_surface_disconnected_polygons              | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((1 1 0,2 1 0,2 2 0,1 2 0,1 1 0)))                                                                                                                      |  ![Screenshot from 2025-05-21 17-19-31](https://github.com/user-attachments/assets/909293eb-e24a-4593-8998-8eb6cbf34365)     |  
| invalid_surface_invalid_intersection_vertex_edge    | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((0.5 0.5 0,2 1 0,2 2 0,1 2 0,0.5 0.5 0)))                                                                                                                | ![Screenshot from 2025-05-21 17-12-52](https://github.com/user-attachments/assets/67035568-5a18-433d-b450-6906c43465a6)      |  
| invalid_surface_invalid_intersection_vertex_vertex  | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((1 0 0,2 1 0,2 2 0,1 2 0,1 0 0)))                                                                                                                        | ![Screenshot from 2025-05-21 17-13-07](https://github.com/user-attachments/assets/ea1c1288-3239-429b-acaa-744377128b16)       |  
| invalid_surface_invalid_intersection_parallel_edges | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((0 1 0,0.5 0.5 0,1 0 0,2 1 0,2 2 0,1 2 0,0 1 0)))                                                                                                        | ![Screenshot from 2025-05-21 17-13-24](https://github.com/user-attachments/assets/c8d158f6-ff5f-4ee9-920c-b8055502eeea)       |  
| invalid_surface_invalid_intersection_operlapping_faces | POLYHEDRALSURFACE(((0 0 0,1 0.5 0,0.5 1 0,0 0 0)),((0 1 0,1 0 0,2 1 0,2 2 0,1 2 0,0 1 0)))                                                                                                           | ![Screenshot from 2025-05-21 17-13-45](https://github.com/user-attachments/assets/5f488f7a-464e-4782-8767-4ac2718782bd)      |  
| invalid_surface_invalid_intersection_operlapping_faces2 | POLYHEDRALSURFACE(((0 0 0,1 0 0,0.5 1 0,0 0 0)),((0 1 0,1 0 0,2 1 0,2 2 0,1 2 0,0 1 0)))                                                                                                             | ![Screenshot from 2025-05-21 17-14-04](https://github.com/user-attachments/assets/ba5d7bad-04a1-4fe7-b1b5-b0a819b09b08)      |  
| invalid_surface_invalid_face                        | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((0 1 0,1 0 0,2 2 0,2 1 0,1 2 0,0 1 0)))                                                                                                                  | ![Screenshot from 2025-05-21 17-14-25](https://github.com/user-attachments/assets/61c2c2e4-0960-4fde-9de5-e05b467a66ab)      |  
| invalid_surface_non_manifold_edge                  | POLYHEDRALSURFACE(((0 0 0,1 0 0,0 1 0,0 0 0)),((0 1 0,1 0 0,2 1 0,2 2 0,1 2 0,0 1 0)),((0 1 0,1 0 0,1 1 1,0 1 0)))                                                                                       | ![Screenshot from 2025-05-21 17-14-43](https://github.com/user-attachments/assets/47c1fbeb-f99c-4a4e-a099-14d07ca69af3)      |  
  
Two unit tests are failing and are commented out. Those are dependent on https://github.com/boostorg/geometry/issues/1406

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-21 14:24:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2857953758  

## Pull Request Overview  
  
This PR enhances the validity checking algorithm by introducing support for polyhedral surfaces in line with the OGC Simple Features Specification. Key changes include:  
- Adding several new failure types for polyhedral surfaces (e.g. collinear, non-coplanar points, inconsistent orientation, etc.).  
- Extending the test suite in is_valid.cpp with comprehensive cases for various valid and invalid polyhedral surfaces.  
- Updating include directives to incorporate polyhedral surface support in the core algorithm implementation.  
  
### Reviewed Changes  
  
Copilot reviewed 4 out of 4 changed files in this pull request and generated 2 comments.  
  
| File | Description |  
| ---- | ----------- |  
| test/algorithms/is_valid.cpp | Adds polyhedral surface test cases and minor documentation updates. |  
| include/boost/geometry/algorithms/validity_failure_type.hpp | Introduces new failure codes specific to polyhedral surfaces. |  
| include/boost/geometry/algorithms/detail/is_valid/implementation.hpp | Adds header inclusion for polyhedral surface implementation. |

📁 test/algorithms/is_valid.cpp

```diff
  14 | #define BOOST_TEST_MODULE test_is_valid
  15 | #endif
```

> Username: Copilot  
> Created_at: 2025-05-21 14:24:55 UTC  
> Updated_at: 2025-05-21 14:24:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100439443  

[nitpick] If the BOOST_GEOMETRY_ALLOW_INCONSISTENT_ORIENTATION macro is no longer needed, consider removing it; otherwise, add a clarifying comment explaining when or why it should be enabled.  
```diff  
+// The BOOST_GEOMETRY_ALLOW_INCONSISTENT_ORIENTATION macro allows the library to process  
+// geometries with inconsistent orientation. Enable this macro only if your use case  
+// involves such geometries and you are aware of the potential implications.  
```

> Username: vissarion  
> Created_at: 2025-05-21 14:33:15 UTC  
> Updated_at: 2025-05-21 14:33:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100458384  

I used this macro to test validity (apart from orientation) for some cases. If the orientation is the only failure then `correct` algorithm can fix it. This could be changed when implementing `correct`.

---

📁 test/algorithms/is_valid.cpp

```diff
1704 |+         invalid_surface_invalid_intersection_vertex_edge);
1705 |+     //BOOST_CHECK(!bg::is_valid(invalid_surface_invalid_intersection_vertex_edge, failure));
1706 |+     //BOOST_CHECK(failure == bg::failure_invalid_intersection);
```

> Username: Copilot  
> Created_at: 2025-05-21 14:24:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100439450  

The tests for invalid intersection cases (vertex-edge and vertex-vertex) are commented out; please either remove these commented lines or add a comment indicating why these tests are temporarily disabled to avoid confusion.  
```diff  
+    //BOOST_CHECK(failure == bg::failure_invalid_intersection);  
+    // Test disabled due to unresolved issue: https://github.com/boostorg/geometry/issues/1406  
```


---

## Review 2 [Commented]

> Username: tinko92  
> Created_at: 2025-05-21 15:14:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2858143037  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 131 |+                 if (boost::geometry::equals(point2, *prev))
 132 |+                 {
 133 |+                     // valid intersection and inconsistent orientation
```

> Username: tinko92  
> Created_at: 2025-05-21 15:14:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100553894  

Should this be ~~in~~consistent?


---

## Review 3 [Commented]

> Username: tinko92  
> Created_at: 2025-05-21 15:50:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2858164352  

I noted some points inline, mostly minor but importantly (imho) the question of separating some computations into strategy.  
  
A minor point, this PR should probably include the removal of  https://github.com/boostorg/geometry/blob/d2e5137f4070d8bbcd8ab150ad4fe42713c8ceb3/include/boost/geometry/geometries/polyhedral_surface.hpp#L47

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 100 |+         }
 101 |+         return false; // All points are collinear
 102 |+     }
```

> Username: tinko92  
> Created_at: 2025-05-21 15:19:32 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100563707  

I think by the library design philosophy and also practically, if one wants to be able to plug in a robust computation later, this is a cartesian strategy.

> Username: vissarion  
> Created_at: 2025-05-21 20:24:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2101102203  

Indeed, a lot of computations here are coordinate system spesific and should be moved in strategies. On one hand, I think that polyhedral surface can only be cartesian. But on the other hand, predicates should be moved to stategies anyways for robustness reasons (as you said).

> Username: tinko92  
> Created_at: 2025-05-28 13:43:50 UTC  
> Updated_at: 2025-05-28 13:43:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111961901  

I thought about it. I think, these are equivalent to three calls to a side_strategy for the the XY, XZ, YZ projections respectively. If any of them returns non-zero, they are non-collinear. We already have the side strategy with the correct tolerances for this, https://github.com/boostorg/geometry/blob/d2e5137f4070d8bbcd8ab150ad4fe42713c8ceb3/include/boost/geometry/strategies/cartesian/side_rounded_input.hpp#L34 .  
  
This can short-circuit (if the call for the first projection returns non-zero which it will almost surely in general position, then the other calls are not necessary) and there is probably a lot of common subexpression elimination that the compiler will do.

> Username: vissarion  
> Created_at: 2025-06-04 12:40:25 UTC  
> Updated_at: 2025-06-10 08:18:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126501830  

That reduces the computation to orientation, great. I was thinking using the `project_point_to_plane_2d` to only do one orientation test but I think projecting to coordinate planes as you suggested is better since it does not involve any computations (substitutions, dot products etc).   
I change this part to use the side strategy provided by the umbrella strategy used in is_valid. This uses the side_by_triangle strategy as the default but the user can change it (as in many other cases in the library). At this point I think this is better than creating a new umbrella strategy that has side_rounded_input as a side strategy. We can think of substituting the side_by_triangle by side_rounded_input in general or in as many algorithms as possible as a future step.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  77 |+     static bool are_linearly_dependent(vec3 const& a, vec3 const& b)
  78 |+     {
  79 |+         return qvm::mag_sqr(qvm::cross(a, b)) < tolerance * tolerance;
```

> Username: tinko92  
> Created_at: 2025-05-21 15:22:00 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100570475  

We may want some factor on this bound, I will recheck.

> Username: tinko92  
> Created_at: 2025-05-28 13:44:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111964256  

Adressed in comment: https://github.com/boostorg/geometry/pull/1409?notification_referrer_id=NT_kwDOAQzti7QxNjUzNzY4ODc1MjoxNzYyNDQ1OQ#discussion_r2111961901.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 176 |+         std::ostringstream oss;
 177 |+         oss << boost::geometry::wkt(polygon);
 178 |+         if (m_polygon_projections.find(oss.str()) != m_polygon_projections.end())
```

> Username: tinko92  
> Created_at: 2025-05-21 15:29:26 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100589595  

This seems rather expensive aside from pulling the wkt dependency into this. Can we use the indices of the polygons in their original datastracture as integer keys instead? (I understand that all boost geometry concepts require random access ranges).

> Username: vissarion  
> Created_at: 2025-05-21 20:19:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2101094722  

Yes, that could be done more efficiently, for sure. I have a todo in the code, but probably it is better to implement it in this PR.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 405 |+             {
 406 |+                 if (std::distance(boost::begin(surface), it1) !=
 407 |+                     std::distance(boost::begin(surface), it2))
```

> Username: tinko92  
> Created_at: 2025-05-21 15:30:39 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100592008  

Is this different from it1 != it2?

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 399 |+         boost::ignore_unused(strategy);
 400 |+         bool disconnected = true;
 401 |+         // TODO: improve perofmance by using a spatial index
```

> Username: tinko92  
> Created_at: 2025-05-21 15:30:51 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100592445  

Typo: performance

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  42 |+ 
  43 |+     using point_2d = boost::geometry::model::point
  44 |+         <CT, 2, boost::geometry::cs::cartesian>;
```

> Username: tinko92  
> Created_at: 2025-05-21 15:36:00 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100602838  

Are polyhedral surfaces always cartesian (I didn't see a corresponding constraint in the concept check https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/geometries/concepts/polyhedral_surface_concept.hpp but I may have missed it) and if not should this be generic or is_valid constrained for now?

> Username: barendgehrels  
> Created_at: 2025-05-28 12:58:20 UTC  
> Updated_at: 2025-05-28 12:58:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111793698  

I think it's a valid constraint at least for now

> Username: vissarion  
> Created_at: 2025-05-28 13:05:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111818775  

I added that as a concept constraint together with the constraint on dimension (==3).

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 178 |+         if (m_polygon_projections.find(oss.str()) != m_polygon_projections.end())
 179 |+         {
 180 |+             auto& projection = m_polygon_projections[oss.str()];
```

> Username: tinko92  
> Created_at: 2025-05-21 15:38:28 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100608039  

Minor: This seems to be only copied from, so maybe it can be declared const.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 222 |+             }
 223 |+             // Project the polygon onto the 2D plane defined by v1 and v2
 224 |+             for (auto& point : ring)
```

> Username: tinko92  
> Created_at: 2025-05-21 15:39:45 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100611505  

This point seems to only be read from.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 311 |+         vec3 seg_dir = p2 - p1;
 312 |+         CT denom = dot(normal, seg_dir);
 313 |+         assert(denom != 0); // Ensure the segment is not parallel to the plane
```

> Username: tinko92  
> Created_at: 2025-05-21 15:45:42 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100625348  

Is this necessarily a bug where the program should be halted in a build with asserts? Seems like something that could also happen with e.g. invalid coordinate inputs (e.g. denormal small coords so that product is zero, since normal is not normalised afaics) that should not be a halt but the check returning an is_valid failure.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 215 |+                 set_vec_from_point::apply(*pit, vi);
 216 |+                 auto vol = qvm::dot(normal, vi)- dot(normal, base);
 217 |+                 if (std::abs(vol) > tolerance)
```

> Username: tinko92  
> Created_at: 2025-05-21 15:49:33 UTC  
> Updated_at: 2025-05-21 15:50:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2100632742  

This may be to narrow for e.g. float coordinates in the range of thousand, I think, the bound should probably be scaled to the input magnitude, I will revisit this later when I have more time.

> Username: tinko92  
> Created_at: 2025-05-28 13:52:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111982803  

This is orient3d (see e.g. https://www.[cs.cmu.edu/afs/cs/project/quake/public/code/predicates.c](https://www.cs.cmu.edu/afs/cs/project/quake/public/code/predicates.c)), i.e. the 3D generalisation of side, a determinant involving `*pit`, `base`, `v1`, `v2`. Bound for a tolerant version can be derived the same way as for side_rounded_input, I'll try to do it by the end of the week. Calling orient3d for every point may look more expensive, but I think the compiler will know to pull most unchanging subexpressions out of the loop (same as dot(normal, base) could be pulled out of the loop).

> Username: vissarion  
> Created_at: 2025-06-04 13:22:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126594342  

Exactly, orient3d is equivalent. I think it would be useful to add some explanations and/or references on how the tolerance bounds are derived as comments in `side_rounded_input`.

> Username: tinko92  
> Created_at: 2025-06-06 10:38:39 UTC  
> Updated_at: 2025-06-06 10:46:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2131961127  

I thought about it. I think, we want  
  
```math  
\begin{vmatrix}  
    a_x-d_x & a_y -d_y & a_z - d_z \\  
    b_x-d_x & b_y-d_y & b_z-d_z \\  
    c_x-d_x & c_y-d_y & c_z-d_z  
\end{vmatrix}  
```  
  
Since only one point changes (let's say $a=\text{vi}$), it makes sense to take the first row as cofactors and the minors will be the elements of $n$ (if all points including $a$ were translated by -base before). Then the determinant is   
  
$(a_x-d_x) \times ((b_y-d_y) \times (c_z - d_z) - (b_z - d_z) \times (c_y - d_y)) - (a_y - d_y) \times \ldots + (a_z - d_z) \times \ldots$  
  
Then, I think, the tolerance (based on https://zenodo.org/records/7539355 with rounded input rules, rounding the second order part up because it doesn't matter for this use case):  
  
```math  
12\epsilon \times \left(\left(\left\lvert a_x\right\rvert  + \left\lvert d_x\right\rvert \right) \times \left(\left(\left\lvert b_y\right\rvert  + \left\lvert d_y\right\rvert \right) \times \left(\left\lvert c_z\right\rvert  + \left\lvert d_z\right\rvert \right) + \left(\left\lvert b_z\right\rvert  + \left\lvert d_z\right\rvert \right) \times \left(\left\lvert c_y\right\rvert  + \left\lvert d_y\right\rvert \right)\right) + \left(\left\lvert a_y\right\rvert  + \left\lvert d_y\right\rvert \right) \times \left(\left(\left\lvert b_x\right\rvert  + \left\lvert d_x\right\rvert \right) \times \left(\left\lvert c_z\right\rvert  + \left\lvert d_z\right\rvert \right) + \left(\left\lvert b_z\right\rvert  + \left\lvert d_z\right\rvert \right) \times \left(\left\lvert c_x\right\rvert  + \left\lvert d_x\right\rvert \right)\right) + \left(\left\lvert a_z\right\rvert  + \left\lvert d_z\right\rvert \right) \times \left(\left(\left\lvert b_x\right\rvert  + \left\lvert d_x\right\rvert \right) \times \left(\left\lvert c_y\right\rvert  + \left\lvert d_y\right\rvert \right) + \left(\left\lvert b_y\right\rvert  + \left\lvert d_y\right\rvert \right) \times \left(\left\lvert c_x\right\rvert  + \left\lvert d_x\right\rvert \right)\right)\right)  
```  
  
where $\epsilon$ is **half** of numerical_limits<CT>::epsilon(). This is the same structure as the expanded determinant. If I made no mistake, this will guarantee that the tolerance is sufficiently large such that for all input points that are correctly rounded approximations of actually collinear points, it will find them coplanar.  
  
Looks like a lot of ops but since only a changes, most of the subexpressions should be eliminated in the loop and it should just be around something like ~22 math ops per point.  
  
And this would be the equivalent of side_rounded for planes.


---

## Review 4 [Commented]

> Username: tinko92  
> Created_at: 2025-05-25 13:53:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2866851079  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 243 |+                 {
 244 |+                     boost::geometry::reverse(projected_polygon);
 245 |+                 }
```

> Username: tinko92  
> Created_at: 2025-05-25 13:53:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2106209252  

Is this implictly assuming that   
1) The polygon is convex? If not, three points may be insufficient to determine its orientation. It is not obvious to me from what I can find on the simple feature spec that it has to be.  
2) Its first three points are not collinear? They may be collinear, even for a valid, convex polygon.

> Username: vissarion  
> Created_at: 2025-05-26 07:55:47 UTC  
> Updated_at: 2025-05-26 07:55:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2106774347  

You are right, this is just to test the unit test I have and not general at all. In general, you have to parse the whole polygon to determine its orientation (unless you know the centroid or some other information).

> Username: vissarion  
> Created_at: 2025-05-26 13:26:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2107358886  

Note that the correct orientation is tested by is_valid call to projected polygon below.


---

## Comment 5

> Username: barendgehrels  
> Created_at: 2025-05-26 16:49:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2910259152  

Looks great! I will review it next Wednesday or Thursday.

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 12:56:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874922351  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  36 |+     {}
  37 |+ 
  38 |+     using CT = typename coordinate_type<PolyhedralSurface>::type;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 12:56:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111790279  

Suggestion, `coor_t`? `CT` looks like a template parameter

> Username: vissarion  
> Created_at: 2025-06-04 07:36:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125897145  

I agree, I changed it to `coordinate_type` which seems more common as a use in the library.


---

## Comment 7

> Username: vissarion  
> Created_at: 2025-05-28 12:57:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2916218029  

@tinko92 thanks for the comments. I fixed all but the ones about tolerance and the strategy. I need to think of a unified and consistent solution for those.

---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 12:58:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874926514  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  40 |+     using polygon_3d = typename boost::range_value<PolyhedralSurface>::type;
  41 |+     using ring_3d = typename boost::geometry::ring_type<polygon_3d>::type;
  42 |+     using point_3d = typename boost::range_value<ring_3d>::type;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 12:58:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111793077  

Why not `point_type` ? It delivers the same but we usually use that. You don't need the ring then (but maybe it's needed anyway)


---

## Review 9 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 12:58:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874929511  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  55 |+         >::type;
  56 |+     using ring_2d = typename boost::geometry::ring_type<polygon_2d>::type;
  57 |+     using segment_2d = typename boost::geometry::model::segment<point_2d>;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 12:58:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111794879  

Do we need this many helper types?

> Username: vissarion  
> Created_at: 2025-06-04 07:41:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125907591  

segment_2d is not used any more so I removed. Ring types we can get them from `std::dectype` if you prefer it, I am ok with those as is.

> Username: barendgehrels  
> Created_at: 2025-06-04 18:22:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2127188476  

It's fine. But did you push already? I don't see the updates...


---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:04:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874960345  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 388 |+         boost::ignore_unused(strategy);
 389 |+         bool disconnected = true;
 390 |+         // TODO: improve perfomance by using a spatial index
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:04:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111813607  

Suggestion, comment:  
`This quadratic loop verifies all mutual surfaces for having a valid intersection`  
  
We could use `partition` as well, and more conveniently, so I would also adapt the `TODO`

> Username: vissarion  
> Created_at: 2025-06-04 07:46:49 UTC  
> Updated_at: 2025-06-04 07:46:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125916838  

Now it is not a quadratic loop, see the new comment.


---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:04:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874963268  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 393 |+             for (auto it2 = boost::begin(surface); it2 != boost::end(surface); ++it2)
 394 |+             {
 395 |+                 if (it1 != it2)
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:04:42 UTC  
> Updated_at: 2025-05-28 13:04:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111815405  

Suggestion, `if (it1 == it2) continue`

> Username: vissarion  
> Created_at: 2025-06-04 07:47:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125918137  

Not needed anymore.


---

## Review 12 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:06:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2874974834  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 387 |+     {
 388 |+         boost::ignore_unused(strategy);
 389 |+         bool disconnected = true;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:06:35 UTC  
> Updated_at: 2025-05-28 13:06:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111822685  

Suggestion, call it `connected = false` and set it later to `true`. For me that would read a bit better

> Username: vissarion  
> Created_at: 2025-06-04 08:00:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125949720  

This is also removed. There is a similar structure/logic in `check_polygon_polygon_intersection` where I used your naming approach.


---

## Review 13 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:10:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875001814  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 322 |+     }
 323 |+ 
 324 |+     // Helper function to check if any segment of a ring intersects a polygon
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:10:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111842174  

Do we really support polygons? Or rings? It's not completely clear to me.  
This is checking the outer ring only?

> Username: barendgehrels  
> Created_at: 2025-05-28 13:13:33 UTC  
> Updated_at: 2025-05-28 13:13:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111854146  

OK I see, in this case checking the outer ring should indeed be sufficient


---

## Review 14 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:12:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875009039  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 332 |+     {
 333 |+         auto const& ring1 = boost::geometry::exterior_ring(polygon1);
 334 |+         for (auto it = boost::begin(ring1); it != boost::end(ring1) - 1; ++it)
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:12:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111846994  

Suggestion: `it + 1 != boost::end(ring1)` to avoid empty ring problems


---

## Review 15 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:13:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875015652  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 345 |+             }
 346 |+         }
 347 |+         return true; // Either no intersection or valid intersection
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:13:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111851514  

Maybe it's better to return a `pair<bool,bool>` or a specific structure, i/o one return value and one out parameter

> Username: vissarion  
> Created_at: 2025-06-04 08:25:34 UTC  
> Updated_at: 2025-06-04 08:25:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2125998967  

Now all functions in `valid_intersection` return `intersection_type`, I think it is easier to follow the code.


---

## Review 16 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:14:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875023303  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 179 |+ 
 180 |+         // Check if the polygon has been projected before
 181 |+         if (m_polygon_projections[polygon_index].projected_polygon.outer().size() > 0)
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:14:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111857172  

Suggestion, `!empty`

> Username: vissarion  
> Created_at: 2025-06-04 08:32:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126013092  

I used the `boost::geometry::is_empty` function.


---

## Review 17 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:15:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875030926  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 190 |+         else
 191 |+         {
 192 |+             auto const& ring = boost::geometry::exterior_ring(polygon);
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:15:23 UTC  
> Updated_at: 2025-05-28 13:15:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111862382  

So this block does really the projection. Nice.  
I think it's good to extract it from this class - and even to another source.  
Such that it is useful on its own, and can be unit tested on its own.

> Username: vissarion  
> Created_at: 2025-06-04 13:43:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126641993  

I agree, would you mind leaving it as future work to see also what other algorithms could use it? As it is now there are a lot of shared variables with the rest of the code as well as errors related to is_valid.

> Username: barendgehrels  
> Created_at: 2025-06-08 07:51:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134514282  

But exactly that (shared variables) will makes it harder to extract it later as well...  
  
A generic polygon projection from 3d to 2d plane would be great. We could then immediately implement the surface-area algorithm on a 3d surface...   
  
It would be a generic algorithm, every patch (surface) would be a separate 2D polygon. Or is it different than what you do here (I couldn't look into all the details yet).  
  
Anyway - I'm fine having it as a next PR - I'm only afraid that it would not really safe time.


---

## Review 18 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:15:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875033100  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 192 |+             auto const& ring = boost::geometry::exterior_ring(polygon);
 193 |+             constexpr bool is_ring_open = boost::geometry::closure<ring_3d>::value
 194 |+                 == boost::geometry::open;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:15:40 UTC  
> Updated_at: 2025-05-28 13:15:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111863784  

This needs to be done only once, we can do it at the top

> Username: tinko92  
> Created_at: 2025-05-28 13:57:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111994492  

Since this is constexpr, this is only done once at compile time, there may be merit to scoping it for separation of logic,.

> Username: barendgehrels  
> Created_at: 2025-05-28 18:33:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2112521075  

Agreed - but the whole list of types is also at the top (not scoped). This one fits there.


---

## Review 19 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:16:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875035129  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 195 |+ 
 196 |+             if ((boost::size(ring) < 3 && is_ring_open) ||
 197 |+                 (boost::size(ring) < 4 && !is_ring_open))
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:16:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111865160  

We have also the `min_points` functionality for this

> Username: vissarion  
> Created_at: 2025-06-04 08:38:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126026284  

Thanks, I used that, it simplified the code.


---

## Review 20 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:16:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875040022  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 224 |+                 }
 225 |+             }
 226 |+             // Project the polygon onto the 2D plane defined by v1 and v2
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:16:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111868495  

Looks interesting. I'm not experienced in this so cannot really comment.


---

## Review 21 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:17:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875044454  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 229 |+                 vec3 p;
 230 |+                 set_vec_from_point::apply(point, p);
 231 |+                 auto projected_p = project_point_to_plane_2d(v1, v2, p);
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:17:24 UTC  
> Updated_at: 2025-05-28 13:17:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111871742  

We can push back in one go, or move/emplace it, or make it const.

> Username: vissarion  
> Created_at: 2025-06-04 08:57:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126072920  

I changes to `projected_polygon.outer().emplace_back(project_point_to_plane_2d(v1, v2, p));`  
that should avoid copies


---

## Review 22 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:17:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875047459  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 230 |+                 set_vec_from_point::apply(point, p);
 231 |+                 auto projected_p = project_point_to_plane_2d(v1, v2, p);
 232 |+                 projected_polygon.outer().push_back(projected_p);
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:17:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111873929  

If projected polygons contain only outers, shouldn't we make it rings then?

> Username: vissarion  
> Created_at: 2025-06-04 13:44:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2126645462  

I added a comment and a unit test that we have to support polygons with holes better. The standard is not very clear to me on how to handle those cases, so I would leave it for a next PR.


---

## Review 23 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:18:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875050265  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 239 |+             }
 240 |+ 
 241 |+             // Store the projection for future use
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:18:17 UTC  
> Updated_at: 2025-05-28 13:18:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111875844  

Suggestion, `Cache the projection for next calls`


---

## Review 24 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-28 13:18:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875051499  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 247 |+         CT dot_product = dot(normal, base);
 248 |+         CT sign1 = dot(normal, p1) - dot_product;
 249 |+         CT sign2 = dot(normal, p2) - dot_product;
```

> Username: barendgehrels  
> Created_at: 2025-05-28 13:18:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2111876678  

This can all be const


---

## Comment 25

> Username: barendgehrels  
> Created_at: 2025-05-28 13:20:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2916317626  

> Looks great! I will review it next Wednesday or Thursday.  
  
I will continue tomorrow. It looks good but I had many suggestions already...

---

## Review 26 [Commented]

> Username: tinko92  
> Created_at: 2025-05-28 14:03:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2875251252  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 411 |+                     else if (intersection == intersection_type::valid)
 412 |+                     {
 413 |+                         disconnected = false;
```

> Username: tinko92  
> Created_at: 2025-05-28 14:03:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2112008579  

Does this check work? My understanding from the specification is that the entire polyhedral surface must be connected, is this not different from any pair of polygons being connected? If my understanding of the standard is correct, this needs a BFS.

> Username: vissarion  
> Created_at: 2025-05-28 19:13:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2112580749  

You are right. Currently, the check only checks that there is no disconnected polygon. More work is needed to check for connectedness of the surface.


---

## Comment 27

> Username: barendgehrels  
> Created_at: 2025-06-04 18:20:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2940972260  

> > Looks great! I will review it next Wednesday or Thursday.  
>   
> I will continue tomorrow. It looks good but I had many suggestions already...  
  
So I didn't continue tomorrow - but great that you processed our comments! Thanks! I plan now to continue this weekend.

---

## Comment 28

> Username: vissarion  
> Created_at: 2025-06-04 19:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2941211076  

I refactor the code addressing almost all of the comments. Some things are left as todo for next PR(s). What is missing from this PR is the predicates (places in the code that use tolerance) that should be written as orientation checks implemented in strategies. I will work on it in the next days.

---

## Review 29 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:40:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908219499  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 109 |+ 
 110 |+     template <int I, int J, typename Strategy>
 111 |+     static bool affinely_independent(point_3d const& p1, point_3d const& p2, point_3d const& p3,
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:40:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134500746  

Suggestion for name: `is_affinely_independent`


---

## Review 30 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:41:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908222596  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 118 |+     }
 119 |+ 
 120 |+     // Try to find 3 non-collinear points
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:41:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134503714  

in any plane?

> Username: vissarion  
> Created_at: 2025-06-12 08:34:39 UTC  
> Updated_at: 2025-06-12 08:34:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2142063220  

3 non-colinear points will define a plane. Later in the code we check if the rest of the points of the polygon lay on this plane.


---

## Review 31 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:54:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908238981  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 125 |+         for (auto pit1 = points_begin(polygon); pit1 != points_end(polygon); ++pit1)
 126 |+         {
 127 |+             for (auto pit2 = pit1; pit2 != points_end(polygon); ++pit2)
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:54:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134516181  

Shouldn't it start with `pit + 1` (and below as well)? It now compares itself several times.


---

## Review 32 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:56:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908239673  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 131 |+                     if (affinely_independent<0, 1>(*pit1, *pit2, *pit3, strategy)
 132 |+                         || affinely_independent<0, 2>(*pit1, *pit2, *pit3, strategy)
 133 |+                         || affinely_independent<1, 2>(*pit1, *pit2, *pit3, strategy))
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:56:13 UTC  
> Updated_at: 2025-06-08 07:56:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134516861  

Suggestion, add comment `One three-point-set is already sufficient, with such three independent points a plane can be constructed` (or similar).


---

## Review 33 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:57:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908240194  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 144 |+     }
 145 |+ 
 146 |+     static point_2d project_point_to_plane_2d(vec3 const& v1, vec3 const& v2, vec3 const& p)
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:57:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134517286  

Without comments, and drawings, and much experience, it's hard to follow this...


---

## Review 34 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 07:58:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908242631  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 158 |+         if (boost::geometry::closure<ring_2d>::value == boost::geometry::closed)
 159 |+         {
 160 |+             ring.pop_back(); // Remove the closing point to make it open
```

> Username: barendgehrels  
> Created_at: 2025-06-08 07:58:05 UTC  
> Updated_at: 2025-06-08 07:58:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134518778  

We have also an iterator doing this automatically. It closes it automatically (which is similar - but you can then always skip the last point).  
That avoids the need for a copy of the ring.

> Username: vissarion  
> Created_at: 2025-06-12 09:15:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2142140771  

Thanks, I will use `closing_iterator`.


---

## Review 35 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:01:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908244364  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 167 |+                 if (boost::geometry::equals(point1, point2))
 168 |+                 {
 169 |+                     visitor.template apply<failure_duplicate_points>();
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:01:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134520243  

Can't this check be made right at the beginning, before even iterating?  
What are point1 and point2? Is it a segment?  
Can we then rename them to `segment_point1` or just `s1` with a comment?


---

## Review 36 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:02:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908244914  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 149 |+     }
 150 |+ 
 151 |+     template <typename VisitPolicy>
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:02:38 UTC  
> Updated_at: 2025-06-08 08:02:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134520684  

So what does it do exactly? It finds if the segment point1-point2 overlaps with any segment of the outer ring of the polygon?

> Username: vissarion  
> Created_at: 2025-06-12 14:19:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2142901429  

To check intersection of a segment point1-point2 and a polygon (all in 3D) it computes the intersection of the segment with the plane of the the polygon and then checks whether this projected intersection point overlaps with the projected polygon.


---

## Review 37 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908245642  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 212 |+         if (!boost::geometry::is_empty(m_polygon_projections[polygon_index].projected_polygon))
 213 |+         {
 214 |+             auto const& projection = m_polygon_projections[polygon_index];
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:04:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134521307  

Shouldn't we project all polygons? Are there cases where projecting a polygon is unnecessary work?  
If we project all of them, we could do that at the beginning.  
  
But probably there are conditions that we can stop already, so then this approach is better indeed. That could use a comment.


---

## Review 38 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:05:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908245851  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 222 |+         {
 223 |+             // TODO: Support interior rings
 224 |+             ring_3d const ring = boost::geometry::exterior_ring(polygon);
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:05:04 UTC  
> Updated_at: 2025-06-08 08:05:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134521450  

It's a copy...


---

## Review 39 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:17:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908251599  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  73 |+     using point_3d = typename boost::geometry::point_type<ring_3d>::type;
  74 |+     using segment_3d = typename boost::geometry::model::segment<point_3d>;
  75 |+     using set_vec_from_point = typename strategy::transform::detail::matrix_transformer::
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:17:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134525655  

The name is not 100% clear to me.  
  
But (I see now) we have that already for a long time.  
It sets a vector from a point?  
Anyway, the `using` clause is fine then...


---

## Review 40 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:18:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908251963  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 189 |+     }
 190 |+ 
 191 |+     // Check if a segment intersects a polygon in 3D space
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:18:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134526079  

So also here `point1` and `point2` form a segment, it would be good to make that clear.


---

## Review 41 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:19:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908252429  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 194 |+                                                          point_3d const& point2,
 195 |+                                                          polygon_3d const& polygon,
 196 |+                                                          size_t const& polygon_index,
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:19:18 UTC  
> Updated_at: 2025-06-08 08:19:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134526490  

-> `std::size_t` and for this one the `&` is not necessary (neither harmful but we never do it). `const` we often do neither but it would actually be good so that can be kept.


---

## Review 42 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:20:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908252943  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 201 |+         vec3 p2;
 202 |+         set_vec_from_point::apply(point1, p1);
 203 |+         set_vec_from_point::apply(point2, p2);
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:20:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134526967  

Why not `v1` and `v2` here, if these are vectors?   
And then below you have another set of `v1` and `v2`, it's not clear to me...


---

## Review 43 [Commented]

> Username: barendgehrels  
> Created_at: 2025-06-08 08:21:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-2908253963  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 274 |+         coordinate_type const dot_product = dot(normal, base);
 275 |+         coordinate_type const sign1 = dot(normal, p1) - dot_product;
 276 |+         coordinate_type const sign2 = dot(normal, p2) - dot_product;
```

> Username: barendgehrels  
> Created_at: 2025-06-08 08:21:44 UTC  
> Updated_at: 2025-06-08 08:21:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2134527721  

The `p1` and `p2` are calculated long before...  
Would it be possible to move that to here? (And rename them to something vector-like)?


---

## Comment 44

> Username: vissarion  
> Created_at: 2025-06-12 15:22:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-2967279788  

@barendgehrels thanks for you comments! I addressed most of them. I need to work more on this PR. Apart from the orient3d some other simplifications can be implemented (the unaddressed comments of Barend are related to those simplifications). I will work on it next week.

---

## Comment 45

> Username: vissarion  
> Created_at: 2025-07-10 14:47:44 UTC  
> Updated_at: 2025-07-10 14:48:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-3057786229  

I have updated this PR. Now strategies are used (with 2d and 3d orientation predicates) and there are no constructions on new geometries apart from coordinate projections (that are not that vulnerable to numerical errors). Also a new algorithm for segment-polygon intersection in 3D that only uses orientation predicates is implemented.

---

## Review 46 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 17:59:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006737940  

📁 include/boost/geometry/strategies/relate3/cartesian.hpp

```diff
   6 |+ 
   7 |+ // Licensed under the Boost Software License version 1.0.
   8 |+ // http://www.boost.org/users/license.html
```

> Username: barendgehrels  
> Created_at: 2025-07-10 17:59:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198359759  

AFAIK we don't use this two-line version.  
Also, the other file added contains:  
```  
// Use, modification and distribution is subject to the Boost Software License,  
// Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
// http://www.boost.org/LICENSE_1_0.txt)  
```  
  
I'm OK with the shorter version if:  
* it is used in other libraries too (do you have a reference)  
* it is consistent within this PR  
  
and then we can modify it in all our sources (maybe in one go)

> Username: vissarion  
> Created_at: 2025-07-11 12:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2200573901  

Thanks for noticing. Actually, there are ~300 files in BG that use the shorter version, while ~1700 that use the longer one. Other boost projects use slightly different versions e.g.  
```  
Distributed under the Boost Software License, Version 1.0.  
(See accompanying file LICENSE.md or copy at http://boost.org/LICENSE_1_0.txt)  
```  
used by `hana` and a similar one by `beast`.  
  
So for this PR I will use the longer one and we have to decide how to proceed with all the sources.

> Username: barendgehrels  
> Created_at: 2025-07-13 08:29:39 UTC  
> Updated_at: 2025-07-13 08:30:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2203233356  

Thanks. And wow... so we've 3 versions indeed, starting with either  
```  
// Distributed under the Boost Software License, Version 1.0.  
// Licensed under the Boost Software License version 1.0.  
// Use, modification and distribution is subject to the Boost Software License,  
```  
and in the whole Boost there are 103 versions (some caused by different way of commenting, but still 50 then)  
  
Actually - why did we start using the version starting with "Licensed"? Because we're the only ones... Was this prescribed by Oracle?  
  
By far the most frequent I see (12411 times) the one you mention with `Distributed under the Boost Software License`  
  
Let's go for that one. And indeed we can have the following lines together (because we use the 100 col. ruler).  
  
I actually also like the ones adding the repository (as done in `url`) and documentation (as done in `unordered`).  
  
So I would propose:  
```  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
// Official repository: https://github.com/boostorg/geometry  
// Documentation: http://www.boost.org/libs/geometry  
```

> Username: vissarion  
> Created_at: 2025-07-16 13:32:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2210451057  

> Actually - why did we start using the version starting with "Licensed"? Because we're the only ones... Was this prescribed by Oracle?  
  
I do not think so, probably it happened accidentally.   
  
>So I would propose:  
```  
// Distributed under the Boost Software License, Version 1.0.  
// (See accompanying file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
// Official repository: https://github.com/boostorg/geometry  
// Documentation: http://www.boost.org/libs/geometry  
```  
I agree.


---

## Review 47 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:00:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006740134  

📁 include/boost/geometry/strategies/cartesian/side_3d_rounded_input.hpp

```diff
   1 |+ // Boost.Geometry (aka GGL, Generic Geometry Library)
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:00:36 UTC  
> Updated_at: 2025-07-10 18:00:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198361087  

we don't need `aka GGL` anymore, in new sources I always omit it.


---

## Review 48 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:00:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006740923  

📁 include/boost/geometry/strategies/cartesian/side_3d_rounded_input.hpp

```diff
   6 |+ 
   7 |+ // This file was modified by Oracle on 2023.
   8 |+ // Modifications copyright (c) 2023 Oracle and/or its affiliates.
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:00:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198361542  

Should be `2025`


---

## Review 49 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:01:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006741828  

📁 include/boost/geometry/strategies/cartesian/side_3d_rounded_input.hpp

```diff
  59 |+         coor_t const det = (p_x - p1_x) * ((p2_y - p1_y) * (p3_z - p1_z) - (p3_y - p1_y) * (p2_z - p1_z))
  60 |+                          - (p_y - p1_y) * ((p2_x - p1_x) * (p3_z - p1_z) - (p3_x - p1_x) * (p2_z - p1_z))
  61 |+                          + (p_z - p1_z) * ((p2_x - p1_x) * (p3_y - p1_y) - (p3_x - p1_x) * (p2_y - p1_y));
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:01:19 UTC  
> Updated_at: 2025-07-10 18:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198362093  

Nice... it could profit from some ASCII art


---

## Review 50 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:03:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006746119  

📁 include/boost/geometry/strategies/cartesian/side_rounded_input.hpp

```diff
  45 |+         }
  46 |+         return geometry::get<0>(point);
  47 |+     };
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:03:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198364772  

So this converts runtime to compile time... that's probably fine (though we usually don't need it).  
Nit: the trailing `;` can go

> Username: vissarion  
> Created_at: 2025-07-11 12:47:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2200645244  

My question here is: what to do if `i>2`? Should we throw an exception or silently return the 0-th coordinate. AFAIU BG leans towards the second choice.


---

## Review 51 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:04:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006750871  

📁 include/boost/geometry/strategies/relate3/cartesian.hpp

```diff
  28 |+ public:
  29 |+ 
  30 |+     // side
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:04:09 UTC  
> Updated_at: 2025-07-10 18:04:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198367706  

this can go - the names are clear enough ;-)


---

## Review 52 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:05:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006757009  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  57 |+ private :
  58 |+     std::stack<size_t> m_stack;
  59 |+     std::vector<bool> m_visited;
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:05:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198371999  

can you include `<stack>` and `<vector`> ?  
also we use `std::size_t` normally


---

## Review 53 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-10 18:06:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3006759240  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 117 |+     }
 118 |+ 
 119 |+     return (cut == 1.0) ? 1 : 0;
```

> Username: barendgehrels  
> Created_at: 2025-07-10 18:06:03 UTC  
> Updated_at: 2025-07-10 18:06:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2198373550  

Is `==` to compare doubles safe enough here?

> Username: vissarion  
> Created_at: 2025-07-11 13:08:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2200690956  

It is not, I will change it to use integer arithmetic only.


---

## Comment 54

> Username: barendgehrels  
> Created_at: 2025-07-10 18:08:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-3058432556  

Cool changes, thanks for the splits - I'll try to finish my review this weekend

---

## Review 55 [Approved]

> Username: tinko92  
> Created_at: 2025-07-15 06:35:39 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3016347572  

Thanks for all the work on incorporating the requested changes! This looks good to me. I left some comments on formatting (with diffs, see attached txt files in comments) and compiler warnings, but nothing that would require another review as far as I can see, so I'm marking it as approve.

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 582 |+                         std::back_inserter(intersecting_boxes));
 583 |+ 
 584 |+             for (size_t i = 0; i < remaining_count-1; ++i)
```

> Username: tinko92  
> Created_at: 2025-07-14 13:16:15 UTC  
> Updated_at: 2025-07-15 06:36:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2204923474  

(Compiler warning) This causes a signed/unsigned comparison warning for me when running the test suite, which goes away for me when `i` is `int`. Diff, which also includes spaces for the -1 attached.   
[polyhedral_surface.hpp.txt](https://github.com/user-attachments/files/21226752/polyhedral_surface.hpp.txt)

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  29 |+ {
  30 |+ 
  31 |+ struct polygon_frontier_type : public std::stack<size_t>
```

> Username: tinko92  
> Created_at: 2025-07-14 13:35:47 UTC  
> Updated_at: 2025-07-15 06:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2204966364  

Is this inheritance intentional or from a previous draft, since the type also has a stack member?

> Username: vissarion  
> Created_at: 2025-07-16 12:32:32 UTC  
> Updated_at: 2025-07-16 12:32:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2210250162  

It is a leftover, thanks.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 166 |+ 
 167 |+     template <typename Strategy>
 168 |+     static std::tuple<int,int,int> affinely_independent(point_3d const& p1,
```

> Username: tinko92  
> Created_at: 2025-07-14 13:51:07 UTC  
> Updated_at: 2025-07-15 06:36:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2205001551  

(Formatting) Missing spaces in type arguments.

---

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 184 |+ 
 185 |+     template <typename Strategy>
 186 |+     static std::tuple<int,int,int> find_non_collinear_points(polygon_3d const& polygon,
```

> Username: tinko92  
> Created_at: 2025-07-14 13:51:13 UTC  
> Updated_at: 2025-07-15 06:36:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2205001757  

(Formatting) Missing spaces in type arguments.


📁 test/geometries/polyhedral_surface.cpp

```diff
  60 |     // clear polyhedral surface
  61 |     bg::clear(polyhedral1);
  62 |     BOOST_CHECK_EQUAL(boost::size(polyhedral1), 0);
```

> Username: tinko92  
> Created_at: 2025-07-14 13:17:47 UTC  
> Updated_at: 2025-07-16 12:00:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2204926505  

(Compiler warning) This gives me a signed/unsigned comparison warning when running the test suite, also for other lines, diff attached  
[Uploading polyhedral_surface.cpp.txt]()  
.

---

📁 test/geometries/polyhedral_surface.cpp

```diff
  85 |     BOOST_CONCEPT_ASSERT( (bg::concepts::ConstPolyhedralSurface<const_polyhedral_t>) );
  86 | 
  87 |+     /* The following tests should fail
```

> Username: tinko92  
> Created_at: 2025-07-15 06:27:57 UTC  
> Updated_at: 2025-07-15 06:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2206518590  

Maybe the following could go into two individual files with a compile-fail in the Jamfile, like e.g. https://github.com/boostorg/geometry/blob/d2e5137f4070d8bbcd8ab150ad4fe42713c8ceb3/test/concepts/Jamfile#L24 .


---

## Comment 56

> Username: vissarion  
> Created_at: 2025-07-16 13:26:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-3078616050  

Thanks for your reviews! I addressed all the comments.   
(However, I think @barendgehrels didn't finish his review yet)

---

## Review 57 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:00:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025932139  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  73 |+ // - 3 if the segment intersects the polygon at an edge
  74 |+ // Reference: R.J.Segura, F.R.Feito - An algorithm for determining intersection segment-polygon in
  75 |+ // 3D, Computers & Graphics 1998
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:00:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2210988308  

👍


---

## Review 58 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:04:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025956359  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 116 |+ 
 117 |+         if ((sign1 == sign2) && (sign2 == sign3)) // Intersects inside p_a, p_b, p_c
 118 |+             cut += 2 * sign; // inside
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:04:43 UTC  
> Updated_at: 2025-07-16 17:04:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2211005665  

This looks much better with the integer i/o the void, thanks!  
  
I have to mention now the style: we should either embrace any if in `{}` or put it on the same line.  
  
Also, the inner parentheses are not needed.  
  
Suggestion put the comment before, make it a one-liner, and omit the redundant braces:  
```  
    // Intersects inside p_a, p_b, p_c -> inside  
    if (sign1 == sign2 && sign2 == sign3) cut += 2 * sign;  
```  
(in the first, combining `&&` with `||`, the some bracing is still needed)


---

## Review 59 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:04:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025957453  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
  99 |+         int sign1 = strategy.side3().apply(s1, p_a, s2, p_b);
 100 |+         int sign2 = strategy.side3().apply(s1, p_c, p_b, s2);
 101 |+         int sign3 = strategy.side3().apply(s1, p_a, p_c, s2);
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:04:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2211006579  

All signs can be made `const`


---

## Review 60 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:07:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025969590  

📁 include/boost/geometry/algorithms/detail/is_valid/polyhedral_surface.hpp

```diff
 279 |+         auto const& p1 = polygon.outer()[projection.p1_idx];
 280 |+         auto const& p2 = polygon.outer()[projection.p2_idx];
 281 |+         auto const& p3 = polygon.outer()[projection.p3_idx];
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:07:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2211014788  

officially we should use `exterior_ring` i/o `outer` - but because it's a local usage, it's guaranteed our model so it's fine.


---

## Review 61 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:08:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025972266  

📁 include/boost/geometry/algorithms/detail/overlay/graph/assign_counts.hpp

```diff
  97 | 
  98 |-     auto assign_right_incoming_for = [&turn, &assign_for](counts_per_op_t const& op1, counts_per_op_t op2)
  98 |+     auto assign_right_incoming_for = [&assign_for](counts_per_op_t const& op1, counts_per_op_t op2)
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:08:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2211016526  

Thanks for the side activity ;-)


---

## Review 62 [Commented]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:08:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025973559  

📁 include/boost/geometry/strategies/cartesian/side_3d_rounded_input.hpp

```diff
  39 |+ //       | (p_x - p1_x)  (p_y - p1_y)  (p_z - p1_z)  |
  40 |+ // det = | (p2_x - p1_x) (p2_y - p1_y) (p2_z - p1_z) |
  41 |+ //       | (p3_x - p1_x) (p3_y - p1_y) (p3_z - p1_z) |
```

> Username: barendgehrels  
> Created_at: 2025-07-16 17:08:50 UTC  
> Updated_at: 2025-07-16 17:08:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#discussion_r2211017480  

👍


---

## Review 63 [Approved]

> Username: barendgehrels  
> Created_at: 2025-07-16 17:09:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1409#pullrequestreview-3025975577  

Big step! Thanks!

---

## Comment 64

> Username: tinko92  
> Created_at: 2025-07-17 03:39:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1409#issuecomment-3082342326  

Thanks for the quick response. The latest force push broke `b2 test`, looks like a missing `git add test/geometries/polyhedral_surface_fail.cpp`.

---
