# #380 Feature/disjoint geo new [Merged]

> Username: vissarion  
> Created at: 2017-02-16 14:44:30 UTC  
> Updated at: 2017-07-06 12:49:22 UTC  
> Merged at: 2017-03-14 18:15:35 UTC  
> Closed at: 2017-03-14 18:15:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/380  

**Title:** Algorithm for disjoint predicate of a segment and a box in spherical and geographic coordinate systems.   
  
_Main idea:_ The algorithm uses an angle test and if it is not enough to judge computes the vertex of the segment (point of maximum latitude) and test if it is covered by the box.   
  
_Main contributions_  
- A formula for computing the longitude of a segment's vertex is implemented.   
- The segment envelope methods are slightly changed to provide more efficient versions needed by disjoint segment box algorithm.   
- New strategies segment_box_spherical and segment_box_geographic are implemented to handle spherical or geographical geometries respectively.

---

## Review 1 [Changes requested]

> Username: barendgehrels  
> Created_at: 2017-02-20 21:48:06 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/380#pullrequestreview-22841208  

Thanks for the PR! I did review most of it. Some tweaking is needed here and there (mainly for the coordinate systems degree/radian)

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  40 | 
  41 |+ template <typename CT, typename CS_Tag>
  42 |+ struct disjoint_segment_box_call_vertex_longitude{
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:32:03 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102095679  

Can you put the curly brace one line lower? (here and below)

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  67 |+                            CT,
  68 |+                            CT alp1,
  69 |+                            Strategy azimuth_strategy)
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:33:07 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102095783  

Should be passed const& I suppose

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 102 |+                                         CT vertex_lat,
 103 |+                                         CT alp1,
 104 |+                                         Strategy azimuth_strategy)
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:34:11 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102095900  

Same here, const& (I mean mainly the strategy)

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 138 |+         segment_point_type p0, p1;
 139 |+         geometry::detail::assign_point_from_index<0>(segment, p0);
 140 |+         geometry::detail::assign_point_from_index<1>(segment, p1);
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:34:51 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102095958  

These two calls need an extra include

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 171 |+         CT b_lat_min = geometry::get<geometry::min_corner, 1>(box) * math::d2r<CT>();
 172 |+         CT b_lon_max = geometry::get<geometry::max_corner, 0>(box) * math::d2r<CT>();
 173 |+         CT b_lat_max = geometry::get<geometry::max_corner, 1>(box) * math::d2r<CT>();
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:38:08 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102096304  

Ah, we've not yet a get_as_radian here... This is actually not completely right (I think) because you assume coordinates are in degrees, which is not required for this algorithm.

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 210 |+ 
 211 |+         segment_point_type p_vertex_deg(vertex_lon * math::r2d<CT>(),
 212 |+                                         vertex_lat * math::r2d<CT>());
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:40:22 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102096622  

same here


📁 include/boost/geometry/algorithms/detail/envelope/segment.hpp

```diff
 338 |+               typename Box,
 339 |+               typename Strategy
 340 |+             >
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:42:20 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102096857  

We ususally have the layout different for template arguments (i.e. same as for structs {} )


📁 include/boost/geometry/formulas/vertex_longitude.hpp

```diff
  39 |+                            T const lat3,
  40 |+                            T const dlon)
  41 |+     {
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:43:11 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102096955  

I think (here and in other places) this should be T const&, for user defined numeric types.

---

📁 include/boost/geometry/formulas/vertex_longitude.hpp

```diff
  85 |+         CT const sin_alp1 = sin(alp1);
  86 |+         CT const sin_alp0 = sin_alp1 * cos_bet1;
  87 |+         CT const sin_alp2 = sin_alp1 * cos_bet1 / cos_bet2;
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:44:18 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102097085  

Are we sure cos_bet2 != 0 ?


📁 include/boost/geometry/strategies/geographic/disjoint_segment_box.hpp

```diff
  45 |+     template <typename, bool, bool, bool, bool, bool> class Inverse =
  46 |+         geometry::formula::thomas_inverse
  47 |+ >
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:45:27 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102097211  

So this layout is OK

---

📁 include/boost/geometry/strategies/geographic/disjoint_segment_box.hpp

```diff
  75 |+ 
  76 |+         return strategy_type();
  77 |+     }
```

> Username: barendgehrels  
> Created_at: 2017-02-20 21:46:15 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102097302  

This relates to Adam's review about getting strategies


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2017-02-21 01:36:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/380#pullrequestreview-22862456  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 179 |+         azimuth_strategy.apply(lon1, lat1, b_lon_max, b_lat_max, a_b3);
 180 |+ 
 181 |+         bool b0(alp1 > a_b0), b1(alp1 > a_b1), b2(alp1 > a_b2), b3(alp1 > a_b3);
```

> Username: awulkiew  
> Created_at: 2017-02-21 01:29:29 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102116824  

This looks unusual, using assignment operator would be more clear. Plus typically initializations are put in separate lines in BG, no matter how short they are.

---

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 183 |+         // if the box is above (below) the segment in northern (southern)
 184 |+         // hemisphere respectively then there is not intersection
 185 |+         if (~(b0 & b1 & b2 & b3) & (b0 | b1 | b2 | b3))
```

> Username: awulkiew  
> Created_at: 2017-02-21 01:32:39 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r102117056  

Why bitwise operators (after implicit conversion to int) are used here, not logical operators? AFAIU the result would be the same and the intention would be more clear. Or is there some reason?


---

## Comment 3

> Username: vissarion  
> Created_at: 2017-03-01 11:13:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#issuecomment-283313308  

Thanks for the reviews! I addressed your comments. Mainly regarding degree/radian issue. I created get_as_radian functions for boxes (and segments). Now disjoint works for both degree and radian input coordinate systems. However, it does not work correctly for a mixed input (e.g. box in degrees and segment in radians) due to covered_by function. This can be resolved in a future commit if we need it as functionality.

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2017-03-01 11:27:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/380#pullrequestreview-24455749  

📁 include/boost/geometry/core/radian_access_box_segment.hpp

```diff
 119 |+ */
 120 |+ template <std::size_t Index, std::size_t Dimension, typename Geometry>
 121 |+ inline typename fp_coordinate_type<Geometry>::type get_as_radian(Geometry const& geometry)
```

> Username: awulkiew  
> Created_at: 2017-03-01 11:27:39 UTC  
> Updated_at: 2017-03-08 16:37:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#discussion_r103663265  

I would put it in one file with `get_as_radian` for points. It'd be simpler for including and consistent with already implemented `get`.


---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-03-01 13:03:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#issuecomment-283334133  

Thanks, I'm ok with merging. @barendgehrels?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2017-03-14 02:26:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#issuecomment-286303120  

@barendgehrels are you ok with merging this PR?

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2017-03-14 17:37:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/380#issuecomment-286500664  

Sure, I'm OK, my points are addressed.

---
