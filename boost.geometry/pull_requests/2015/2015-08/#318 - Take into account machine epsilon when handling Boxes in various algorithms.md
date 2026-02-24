# #318 Take into account machine epsilon when handling Boxes in various algorithms. [Merged]

> Username: awulkiew  
> Created at: 2015-08-04 23:38:43 UTC  
> Updated at: 2015-08-25 14:58:41 UTC  
> Merged at: 2015-08-19 00:41:26 UTC  
> Closed at: 2015-08-19 00:41:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/318  

This PR replaces the native operators `<`, `>`, `<=`, `>=` used for Box corners with functions from `geometry::math` namespace.  
  
The main problem that is solved by the PR is the incompatibility of `disjoint(Box, Box)` and intersection strategy calculating the intersection of segments when both are used in `partition()`. Currently in some cases the turns (intersection points) may be not calculated because a section corresponding to some number of segments is pruned due to the too restrictive `disjoint(Box, Box)`. See the `union()` and `get_turns()` tests changed/added in this PR.  
  
Currently the library sees the envelope of a geometry as a Box slightly smaller than it should be. For instance it's possible to check spatial relation for some geometry and an envelope of this geometry and the result won't be compatible, e.g. the geometry will be detected as intersecting but an envelope of this geometry as disjoint.  
  
This PR makes the Boxes consistent with other geometries. Since Points are handled WRT epsilon but Boxes aren't, currently it's possible to get inconsistent results checking Boxes and Boxes' corners (Points). E.g. `touches()` could return false for 2 boxes having spatially equal corners (Points).  
  
I also modified the `intersection()` because it should be compatible with `intersects()`/`disjoint()`. Currently when there is very small distance between two boxes (lesser than EPS) the box which is the result of the intersection operation is a tiny Box between the two Boxes passed into the algorithm.  
  
I also saw other places where potentially similar changes could be done but haven't yet investigated:  
- buffer/buffered_piece_collection.hpp  
- distance/segment_to_box.hpp  
- extensions/views/enveloped_view.hpp

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-08-10 15:55:32 UTC  
> Updated_at: 2015-08-13 19:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#discussion_r36647722  

Please omit these spaces  
`else if (max1 < max2)`

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-08-10 15:59:13 UTC  
> Updated_at: 2015-08-13 19:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#discussion_r36648214  

Why is this changed?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2015-08-10 15:59:45 UTC  
> Updated_at: 2015-08-13 19:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#discussion_r36648283  

Perfect!

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-08-10 16:00:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-129507043  

I'm OK with merging this. Thanks for the fix / PR!

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-08-10 17:08:33 UTC  
> Updated_at: 2015-08-13 19:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#discussion_r36656288  

I thought it's a mistake since the order of geometries for which the algorithm could fail to provide the correct result is reversed. So it seems to be more intuitive to output the WKTs in the order for which the algorithm is failing. But now I see that the original order is used in `test_equals`, `test_overlaps` and `test_intersects`) but not in `test_touches`. Furthermore, if the original order is printed out it should probably be easier to find the line where test case is implemented in the file. Do you think I should revert this change?

---

## Comment 6

> Username: barendgehrels  
> Created_at: 2015-08-10 18:04:15 UTC  
> Updated_at: 2015-08-13 19:44:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#discussion_r36663284  

I think it is better to revert it, indeed.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2015-08-13 19:46:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-130818272  

Thanks for the review. Before merging I'm going to wait for Menelaos' opinion.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2015-08-18 20:41:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-132344217  

Is it possible to merge it tomorrow morning? I would like to continue and it is convenient if it is merged by then... Comments from Menelaos can also be processed after merging.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2015-08-19 12:31:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-132573847  

I've merged it but now I think that maybe I shouldn't have. I run the rtree benchmark to see what's the spatial query performance loss and it's quite big. Most certainly it's the same for partition. I 've made some quick tests to see if it's possible to optimize it a bit but I'm not satisfied. Below are the times of `intersects()` spatial queries (so calling `disjoint()`) on my machine (msvc10 /O2 though on linux/gcc I've got similar results):  
  
|  | packed | linear |  
| --- | --- | --- |  
| original, naiive implementation | 0.06 | 0.76 |  
| currently used implementation | 0.25 | 2.06 |  
| optimized `smaller()` | 0.17 | 1.48 |  
| optimized `disjoint()` | 0.10 | 1.17 |  
- optimized `smaller()` first checks if `b <= a` and returns right away if it doesn't (instead of always first checking `equals()` which is very costly operation).  
- optimized `disjoint()` doesn't use `smaller()` or `larger()` but uses raw operators for an enlarged box (e.g. for `max = original_max + abs(original_max) * numeric_limits<ct>::epsilon()`). This is without `std::max(abs_original_max, 1)` which is there in `equals()`, with it the performance is more or less the same as of optimized `smaller()`.  
  
Even with optimized `disjoint()` the queries are 2x slower. My guess is that we could make them faster if the boxes were enlarged during the initialization, e.g. in `envelope()` for non-Box geometries or even better explicitly in the rtree and partition. The latter would probably be faster since the boxes which really have to be enlarged would be, i.e. the boxes of the lowest-level objects (e.g. for the rtree only the leafs' bounding boxes).

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2015-08-19 13:41:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-132603118  

The buffer performance test (using partition heavily) shows a degradation of about 25%   
So slower, but not 2x   
Just to let you know, I've not an opinion about enhancing or reverting. We're not close to release so we can think about what to do.

---

## Comment 11

> Username: awulkiew  
> Created_at: 2015-08-19 15:50:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-132645518  

That's understandable, the rtree benchmark tests specifically the spatial query (basically only data access and predicate checks), in the buffer other things are also done.  
  
I've tested other approach, enlarging the boxes using scaled `epsilon` in `envelope()` and `expand()` for Points and Segments. The `buffer_mp2` test case still passes. The rtree sptial queries time of course is not affected which was the goal. The rtree pack-creation time is increased from `0.40s` to `0.45s`, insertions time using linear algorithm is increased from `1.45s` to `1.95s`. Considering that fast queries are more important than creation I prefer an approach similar to this. Btw, in this case I'm not sure if we could say that the bounding boxes are the **minimum** bounding boxes/rectangles anymore.  
  
I think I could still improve the performance by careful enlarging the bounding boxes only when required, e.g. at the end of the calculation of a bounding box of a collection of geometries (rtree node, partition section, etc.), so once per collection, not for each `envelope()` or `expand()` call. This would be done only in the rtree and partition so if we wanted to use bounding boxes internally in the library in a different way we'd be forced to take special care about them ourselves. But this is exactly how it was in the past, i.e. before this PR was merged. And in this case the boxes created by `envelope()`/`expand()` would still be **minimum** by default.

---

## Comment 12

> Username: awulkiew  
> Created_at: 2015-08-25 14:58:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/318#issuecomment-134613426  

I also discovered that after merging this PR there is one regression when rescaling is disabled. The failing test is `buffer_multi_polygon`, the failures:  
  
```  
.../test_buffer.hpp(251): error: in "test_main_caller( argc, argv )": multipoly_rt_i_d_miter_flat output is empty (unexpected).  
.../test_buffer.hpp(278): error: in "test_main_caller( argc, argv )": multipoly_rt_i_d_miter_flat not as expected.  Expected: 13.6569 Detected: 0 Diff: -13.6569 , -100%  
.../test_buffer.hpp(251): error: in "test_main_caller( argc, argv )": multipoly_rt_i_d_miter_flat_ccw output is empty (unexpected).  
.../test_buffer.hpp(278): error: in "test_main_caller( argc, argv )": multipoly_rt_i_d_miter_flat_ccw not as expected.  Expected: 13.6569 Detected: 0 Diff: -13.6569 , -100%  
```  
  
This PR makes the spatial relations checks for Boxes consistent with checks for other Geometries. So the above shouldn't happen. How are the Boxes used in `buffer(MultiPolygon)`? Could there be some bug there?

---
