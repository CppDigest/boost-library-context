# #936 Fix envelope of polygon containing pole. [Merged]

> Username: awulkiew  
> Created at: 2021-11-09 22:24:53 UTC  
> Updated at: 2022-02-14 16:16:02 UTC  
> Merged at: 2022-02-14 16:16:02 UTC  
> Closed at: 2022-02-14 16:16:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/936  

This PR fixes spherical and geographic envelope for polygons containing poles. Currently the envelope of a polygon is the envelope of the linear ring. If a polygon is a polar cap then the result is incorrect because the envelope should contain the pole even if it's above or below linear ring.  
  
If an envelope covers the whole longitude range then poles are checked to see whether or not they are inside the ring. This check is done with `point_in_poly` strategy with custom `pole_side` strategy to avoid costly calculation done in regular side strategies.  
  
Additional changes:  
- envelope strategy previously used for linear and polygonal geometries as well as multi geometries is divided into three strategies: one for linestring, one for ring and one for range of boxes which is used for multi geometries,  
- `point_iterator` and `segment_iterator` are no longer used (ranges are processed in strategies based on CS),  
- `clockwise_view`, `closed_view` and `closed_clockwise_view` are adapted to geometry concepts based on the input `Range` which allows passing them as geometries into various algorithms,  
- test are added and other that were previously disabled are enabled.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-11-17 17:59:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#issuecomment-971822674  

Thanks! I'm not ready yet. My remarks so far are all minor of course.  
It seems quite a change for such an issue. But you added a useful description to this PR, will continue later.

---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2021-11-19 14:38:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/936#pullrequestreview-811227319  

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
 113 |+         // This strategy shouldn't be called in this case but just in case
 114 |+         // check if segment starts at a pole
 115 |+         if (math::equals(lat, lat1) || math::equals(lat, lat2))
```

> Username: vissarion  
> Created_at: 2021-11-19 13:51:32 UTC  
> Updated_at: 2021-11-19 14:38:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753206247  

shouldn't this be checked before computing `s_lon_diff` even this check is "just in case"

> Username: awulkiew  
> Created_at: 2021-11-19 14:56:23 UTC  
> Updated_at: 2022-01-12 13:08:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753263534  

This condition shouldn't be met aside from some unexpected case. The previous condition `s_vertical` however is perfectly normal. So I'd expect `s_vertical` to be true more often and therefore less conditions tested on average.

---

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
  73 |+ 
  74 |+ 
  75 |+ // Side of pole WRT segment which doesn't contain it.
```

> Username: vissarion  
> Created_at: 2021-11-19 13:54:19 UTC  
> Updated_at: 2021-11-19 14:38:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753208434  

does this function assumes pole is not contained? And what "contain" means? Be on the geodesic or have the pole as one the endpoints?

> Username: awulkiew  
> Created_at: 2021-11-19 14:48:04 UTC  
> Updated_at: 2021-11-19 14:48:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753255266  

Right, technically in de9im sense a segment of a polygon doesn't intersect a pole. In other words a polygon either contains the pole or they are disjoint.

---

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
  80 |+ 
  81 |+     template <typename P>
  82 |+     static inline int apply(P const& p1, P const& p2, P const& p)
```

> Username: vissarion  
> Created_at: 2021-11-19 13:57:59 UTC  
> Updated_at: 2021-11-19 14:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753211180  

is guaranteed that `p` is a pole? If so is it better to name it like that e.g. `pole`.

---

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
 183 |+ template
 184 |+ <
 185 |+     typename Range, typename Box,
```

> Username: vissarion  
> Created_at: 2021-11-19 14:03:09 UTC  
> Updated_at: 2021-11-19 14:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753215100  

According to our guidelines, long template parameter list shall be formatted with one template parameter per line.

---

📁 include/boost/geometry/strategy/spherical/envelope_range.hpp

```diff
 152 |+ 
 153 |+ template <typename Ring, typename PoleWithinStrategy>
 154 |+ inline bool pole_within(bool north_pole, Ring const& ring,
```

> Username: vissarion  
> Created_at: 2021-11-19 14:16:12 UTC  
> Updated_at: 2021-11-19 14:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753224756  

Alternatively, of having a `bool` parameter and an `if` statement you could directly pass `max_latitude` or `min_latitude` as a function parameter and explicitly set it in the function.


📁 test/algorithms/envelope_expand/envelope_on_spheroid.cpp

```diff
2753 |+                   from_wkt<G>("POLYGON((),(0 -80,90 -80,-180 -80,-90 -80,0 -80))"),
2754 |+                   -180, -90, 180, -80);
2755 |+ }
```

> Username: vissarion  
> Created_at: 2021-11-19 14:24:41 UTC  
> Updated_at: 2021-11-19 14:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753231576  

end of line missing

---

📁 test/algorithms/envelope_expand/envelope_on_spheroid.cpp

```diff
2500 |                   0, 10, 50, 45);
2501 |- #if 0
2501 |+ 
```

> Username: vissarion  
> Created_at: 2021-11-19 14:38:24 UTC  
> Updated_at: 2021-11-19 14:38:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r753247249  

great, so now with the new implementation only a) spherical caps and b) rings that involve (contain/pass through/touch) both poles are not supported.  
  
Interestingly, for north poles spherical caps are supported but their complements not but for south poles the opposite holds.

> Username: awulkiew  
> Created_at: 2022-01-19 23:22:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r788226504  

Some of the results may be correct by coincidence.


---

## Comment 3

> Username: vissarion  
> Created_at: 2021-11-19 14:38:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#issuecomment-974127405  

Thanks for this PR! I left some comments.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2022-01-19 23:35:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#issuecomment-1016968712  

@vissarion Thanks for the review! I addressed your comments.  
  
@barendgehrels What do you think about this PR?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2022-01-26 12:38:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#issuecomment-1022160682  

> @vissarion Thanks for the review! I addressed your comments.  
>   
> @barendgehrels What do you think about this PR?  
  
Yes, sorry, I forgot to finish the review. In general I'm fine with it (though I prefer separating fixes and refactoring - this makes it harder to judge).  
  
I'll approve, but there are still some open questions from my side unanswered.

---

## Review 6 [Approved]

> Username: barendgehrels  
> Created_at: 2022-01-26 12:39:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/936#pullrequestreview-808949008  

📁 include/boost/geometry/strategies/envelope/cartesian.hpp

```diff
  83 |+                              || util::is_multi_polygon<Geometry>::value
  84 |+                              || util::is_geometry_collection<Geometry>::value
  85 |+                             > * = nullptr)
```

> Username: barendgehrels  
> Created_at: 2021-11-17 17:50:30 UTC  
> Updated_at: 2022-01-26 12:39:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r751489824  

Why is this algorithm not tag-dispatched anymore, as we usually do?  
(I know it's not changed in this PR - so it is a general question)

> Username: awulkiew  
> Created_at: 2022-01-26 14:25:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r792689076  

This is not algorithm but envelope strategy getter in `cartesian` umbrella strategy.


📁 include/boost/geometry/strategies/envelope/spherical.hpp

```diff
  15 | 
  16 |- #include <boost/geometry/strategy/spherical/envelope.hpp>
  16 |+ #include <boost/geometry/strategy/spherical/envelope.hpp> // Not used, for backward compatibility
```

> Username: barendgehrels  
> Created_at: 2021-11-17 17:51:33 UTC  
> Updated_at: 2022-01-26 12:39:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r751490860  

Why do we still need it?

> Username: awulkiew  
> Created_at: 2022-01-26 14:26:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r792690556  

We probably don't. I'll remove it.


📁 include/boost/geometry/strategy/cartesian/envelope_boxes.hpp

```diff
  26 |+     class state
  27 |+     {
  28 |+         friend cartesian_boxes;
```

> Username: barendgehrels  
> Created_at: 2021-11-17 17:52:11 UTC  
> Updated_at: 2022-01-26 12:39:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r751491471  

Why do you need this friend?

> Username: awulkiew  
> Created_at: 2022-01-26 14:29:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r792693099  

To allow the `cartesian_boxes` strategy to access the members of the `state` and at the same time prevent a caller from accessing them. This way we can be sure that the `state` is not going to be modified outside of this strategy.


📁 include/boost/geometry/strategy/geographic/envelope_range.hpp

```diff
  54 |+                                         <
  55 |+                                             FormulaPolicy, Spheroid, CalculationType
  56 |+                                         >(m_spheroid));
```

> Username: barendgehrels  
> Created_at: 2021-11-17 17:53:33 UTC  
> Updated_at: 2022-01-26 12:39:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r751492831  

Not so nice indentation...

> Username: awulkiew  
> Created_at: 2022-01-26 14:33:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r792697243  

Would this be ok?  
```  
detail::spheroidal_linestring(range, mbr,  
    envelope::geographic_segment  
        <  
            FormulaPolicy, Spheroid, CalculationType  
        >(m_spheroid),  
    expand::geographic_segment  
        <  
            FormulaPolicy, Spheroid, CalculationType  
        >(m_spheroid));  
```  
or maybe this?  
```  
auto const envelope_s = envelope::geographic_segment  
    <  
        FormulaPolicy, Spheroid, CalculationType  
    >(m_spheroid);  
auto const expand_s = expand::geographic_segment  
    <  
        FormulaPolicy, Spheroid, CalculationType  
    >(m_spheroid);  
detail::spheroidal_linestring(range, mbr, envelope_s, expand_s);  
```

> Username: barendgehrels  
> Created_at: 2022-02-02 10:50:51 UTC  
> Updated_at: 2022-02-02 10:50:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r797482830  

Yes, that looks better. Both of them, but for me the last one is the most readable.  
Thank you.

> Username: awulkiew  
> Created_at: 2022-02-08 16:47:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/936#discussion_r801844021  

Done.


---

## Review 7 [Approved]

> Username: vissarion  
> Created_at: 2022-02-14 15:54:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/936#pullrequestreview-881836758  

I am OK. Thanks!

---
