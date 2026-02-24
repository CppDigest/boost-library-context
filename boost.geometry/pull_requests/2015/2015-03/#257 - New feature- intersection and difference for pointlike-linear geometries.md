# #257 New feature: intersection and difference for pointlike/linear geometries [Merged]

> Username: mkaravel  
> Created at: 2015-03-10 15:07:41 UTC  
> Updated at: 2015-05-06 08:11:30 UTC  
> Merged at: 2015-05-02 02:05:18 UTC  
> Closed at: 2015-05-02 02:05:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/257  

In this PR I implement intersection and difference for pointlike/linear geometries.  
  
The algorithm is as follows:  
- For point/linear geometries, simply check if the point intersects with the linear geometry and appropriately output the result of the set operations.  
- For multipoint/segment, check each point of the multipoint for intersection with the segment; appropriately output each point in the result.  
- For multipoint/linear ("linear" here excludes segments), call `bg::partition()` in the multipoint and the segments of the linear geometry to find the common points. Then call the corresponding pointlike/pointlike set operation on the input multipoint and the set of common points, and output the result of this operation.  
  
This PR is targeted for boost 1.59. It requires that PR #244 and PR #256 are merged first.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-04-12 19:54:13 UTC  
> Updated_at: 2015-04-23 07:16:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#discussion_r28206711  

What the last name part `point` stands for? The names are slightly confusing. Are there also other names like `multipoint_segment_notpoint`? If not, then maybe this last part might be removed?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2015-04-12 20:01:39 UTC  
> Updated_at: 2015-04-23 07:16:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#discussion_r28206787  

Do I understand correctly that at this point all points of `multipoint` overlapping the segments of `linear` are in `common_points` vector? And that `multipoint_multipoint_point` is used to filter either the equal or non-equal points from the intput multipoint? If yes, then I think both sets could be generated, or even outputted in the same time, in get_common_points() -> partition. item_visitor could either output the intersecting or disjoint points on the fly. AFAIU there is no need to create a temporary vector and to later filter the points.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-04-28 21:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#discussion_r29290920  

This has been done in analogy with other overlay structs. Take a look at [intersection_insert.hpp](https://github.com/boostorg/geometry/blob/master/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp) for example. There are structs like `intersection_segment_segment_point` and `intersection_linestring_linestring_point`. The same convention is also used in [linear_linear.hpp](https://github.com/boostorg/geometry/blob/master/include/boost/geometry/algorithms/detail/overlay/linear_linear.hpp).  
  
The idea is that the first two geometries in the name are the input geometries while the third indicates the value type of the output container (in this case a container of points).

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-04-28 21:52:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#discussion_r29292573  

Yes, your understanding is correct: `common_points` contains all points of the `multipoint` that are in the intersection of `multipoint` and `linear`.  
  
Let me start by saying that I do understand your concerns. I had the same questions/thoughts when implementing these set operations and finally decided to implement them as you see in this PR for the reasons described below.  
  
Let's consider that this `apply()` method does: it is used to compute either the difference or the intersection of `multipoint` with `linear`. More analysis below.  
  
#### Case 1: Intersection  
  
I agree with you that in this case the visitor could do the job. However there is a draw back in this approach: if the multipoint contains a point `p` (and assume that `p` appears in `multipoint` once) that is contained in many segments of the linear geometry, then `p` will be part of the output multipoint many times (as many times as the number of segments in the linear geometry that contain it). I wanted to avoid that and maintain the property that a point of `multipoint`, that is part of the intersection of the multipoint and the linear geometry, appears in the output as many times as it appears in `multipoint`.  
  
#### Case 2: Difference  
  
Difference is a bit trickier: suppose that `multipoint` contains a point `p` such that it intersects a segment `s1` of `linear` but does not intersect another segment `s2` of `linear`. Then the visitor for partition will discard `p` when considered against `s1` but will keep it when considered against `s2`. So `p` will actually appear in the output which is wrong.  
  
Essentially what I am saying is that in order to correctly implement `difference(multipoint, linear)` using `bg::partition` I first need to compute the intersection of `multipoint` and `linear` and then take the difference of `multipoint` and the intersection computed. This is essentially what the code does. On top of that, the current implementation for difference maintains the same property as intersection: a point of `multipoint` appears in the output (if is part of the output) with the same _multiplicity_ as in the input `multipoint`.  
  
#### Summary  
  
The current approach has been used for two reasons:  
- Algorithm correctness (this applies to the difference algorithm).  
- For a point `p` in the input that is also part of the output, we maintain the invariant that the multiplicity of `p` in the input multipoint stays the same in the output multipoint.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2015-05-02 02:16:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#discussion_r29544139  

Thanks for the explanation!  
Indeed, the property of preserving the points can be useful. Though I'm guessing that in real life it shouldn't be a problem, that a point will intersect several segments in the same time. It'd be possible only for spikes and crossings. And even if it did, the result would be spatially equal to the one that's currently generated. However currently the result seems to be more "correct". I'm wondering if there would be a meaningful difference in performance between the two approaches. We could come back to it in the future if we had some free time.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2015-05-02 02:16:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/257#issuecomment-98290913  

Thanks for the contribution!

---
