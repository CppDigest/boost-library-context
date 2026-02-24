# #386 Use envelope-seg and disjoint-seg/box strategies in various algorithms. [Merged]

> Username: awulkiew  
> Created at: 2017-03-22 20:10:53 UTC  
> Updated at: 2018-01-23 00:13:34 UTC  
> Merged at: 2017-05-04 11:14:39 UTC  
> Closed at: 2017-05-04 11:14:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/386  

- add envelope and disjoint strategies getters into side, winding and intersection strategies  
- pass envelope and disjoint strategies into sectionalize and/or partition in get_turns, disjoint, is_valid and overlay.  
  
One more thing should be done, i.e. forcing `point_on_border` to return the vertex in at least non-cartesian coordinate systems. `point_on_surface` seems to be not used anywhere so there is no problem here. Do you have any thoughts regarding `point_on_border`? Second parameter `true` seems to be passed only in 2 places in the library, i.e. `overlay/intersection_insert.hpp` and `overlay/ring_properties.hpp`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-03-23 19:27:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-288834156  

Thanks for the PR.   
About point_on_border: it is implemented like that for overlay purposes. Returning the last vertex instead will most probably need some changes there, as soon as we want to do overlays with non-cartesian (and we want...). It probably needs a bit more research.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-03-24 01:21:32 UTC  
> Updated_at: 2017-03-27 11:46:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-288907192  

I tried to disable the `midpoint` in `point_on_border()` and some tests started to fail: buffer, intersection, union, difference, overlay.  
  
For now I only checked overlay and would like to get some feedback regarding my findings. There are 2 calls to `point_on_border()` with `midpoint = true` in the library:  
1. https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/intersection_insert.hpp#L341  
2. https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/ring_properties.hpp#L65  
  
In the case of overlay the 2. is relevant (`ring_properties` is also used in buffer so it's possible it's related). The point calculated there is used in 3 places in overlay:  
a) https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/select_rings.hpp#L264  
b) https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp#L50  
c) https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp#L143  
  
In the failing case `case_recursive_boxes_4` union is tested and after disabling `midpoint` the check at place a) starts to give different results, this is how it looks like (midpoint enabled on the left), the point is tested for `within` with the green geometry:  
  
![overlay_union](https://cloud.githubusercontent.com/assets/1226951/24276308/08eb23dc-1036-11e7-9b6c-c57317b23b3c.jpg)  
  
Obviously the result of `within()` is different in both cases. But is `within()` really the correct check? What if the green geometry had a boundry there, a vertex, etc? Would it be possible to prepare such a case where the midpoint would lie on a boundary? Anyway, maybe I'll play with it later and try to break this test case.  
  
For now I tried replacing `within()` with `covered_by()` in all 3 places (a, b, c) because AFAIU this is how rings should be tested since they may touch each other and in c) `within()` Point/Box is checked so it really doesn't make any difference because EPS is not used anyway. This fixes the overlay test. I didn't check other algorithms yet. Before that I'd like to know what do you think? Is there anything I'm missing?  
  
Aside from that, point-in-polygon and point-in-box within/covered_by strategies should be passed into these algorithms. Actually this should be done in all places in the library. For all algorithms used internally.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2017-03-24 07:29:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-288952413  

Thanks for quick and clear research! I am fine with changing it to coveredby, if that fixes the new failures. I think it is not there because when written, coveredby did not exist yet. Also passing strategies is fine of course.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-03-24 16:40:40 UTC  
> Updated_at: 2017-03-27 11:46:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-289075794  

Disabling midpoint (even with `covered_by`) breaks buffer_multi_polygon tests:  
- `multipoly_zonethru_10` slightly different area but it looks the same  
- `multipoly_rt_b` one polygon not included (below)  
- `multipoly_rt_k` one polygon not included  
  
![obraz](https://cloud.githubusercontent.com/assets/1226951/24301449/e50ed3a8-10af-11e7-8f64-0a6d13bde24b.png)  
  
It's caused by the fact that at c) (https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp#L143) The `inner_in_map.point` is tested with `outer.envelope` which seems to be too small to cover (so also contain) the vertex point. Expanding it slightly with `expand_by_epsilon()` here: https://github.com/boostorg/geometry/blob/b320ddf/include/boost/geometry/algorithms/detail/overlay/assign_parents.hpp#L218 fixes the issue.  
  
Btw, we should probably always expand envelopes slightly if they are going to be used with geometries compared with epsilon. Like in this case (c), the purpose is to check `covered_by(point, ring)` and this check is done taking the epsilon into account. If we want to do a simple envelope check before, so `covered_by(point, ring_box)`, then we should expand this envelope to cover the "effective area" of a ring. I've done something like this some time ago in sectionalize to fix an issue. I think it would be possible to generate a buffer case which would fail due to this behavior, even with midpoint enabled.

---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2017-03-27 22:05:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/386#pullrequestreview-29314719  

📁 include/boost/geometry/algorithms/detail/point_on_border.hpp

```diff
 306 |+             >::apply(point, geometry);
 307 |+ }
 308 |+ */
```

> Username: barendgehrels  
> Created_at: 2017-03-27 22:05:35 UTC  
> Updated_at: 2017-04-26 02:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#discussion_r108292081  

This is commented. Maybe we can omit this functionality. Don't think it is ever used, though we can of course not be sure about that. The bool flag was only added for overlay.  
For the rest - changes look OK to me, I did not get into the details but I think it is an enhancement indeed. Thanks.

> Username: awulkiew  
> Created_at: 2017-03-28 14:42:36 UTC  
> Updated_at: 2017-04-26 02:35:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#discussion_r108440890  

Should I remove the related code from the internals and Midpoint parameter, so remove this feature entirely?  
  
What with related but not used anywhere `point_on_surface`?  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/point_on_surface.hpp


---

## Comment 6

> Username: awulkiew  
> Created_at: 2017-04-13 11:35:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-293865078  

@barendgehrels Strategies are supported in set operations entirely. There is only one place where `detail::point_on_border::midpoint_helper` is used:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L66  
which was added in this commit:  
https://github.com/boostorg/geometry/commit/e35a3b1610d61c184bbf810f8d73449d92870957  
After changing this place to use the segment endpoint instead of point in between the tests both in develop and my branch pass. Do you remember the rationale behind this, or a test case which it fixed? I'd like to use the segment endpoint, is that ok?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2017-04-17 15:08:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/386#issuecomment-294505197  

@barendgehrels Not taking into account the change I mentioned above at line:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/overlay/follow.hpp#L66  
this PR is ready for merging.

---
