# #478 Box-segment distance for spherical and geographic coordinate systems [Merged]

> Username: vissarion  
> Created at: 2018-04-24 12:32:21 UTC  
> Updated at: 2019-07-03 09:27:11 UTC  
> Merged at: 2018-07-04 13:03:57 UTC  
> Closed at: 2018-07-04 13:03:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/478  

This PL implements the box-segment distance computation for spherical and geographic coordinate systems (CS).   
  
The CS independent functionality is still in the algorithm which is generalized to support the new  coordinate systems. The CS dependent functionality is implemented in a set of new strategies.   
  
Along the way few fixes in envelope of segment and point-segment distance are performed.   
  
Now, distance should be able to be computed between any pair of geometries and for any CS. A new PL will test this.

---

## Review 1 [Changes requested]

> Username: awulkiew  
> Created_at: 2018-05-10 14:52:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/478#pullrequestreview-119079075  

Thanks for this PR!  
I'm ok in general but I have few remarks about the structure of the code, interfaces of strategies etc. It's possible that I don't understand something and that the way how it's implemented is the best way. But for now I think strategies should be restructured slightly and that the responsibilities of various strategies are mixed up.

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  80 |+     //         2 if disjoint (vertex computed)
  81 |+     template <typename Segment, typename Box, typename Strategy, typename P>
  82 |+     static inline std::size_t apply(Segment const& segment,
```

> Username: awulkiew  
> Created_at: 2018-05-10 13:07:11 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187323746  

enum would be safer here, or taking output value of `bool&` as argument and return bool indicating that value was computed. Unless this integral value is used in some calculation while testing something but AFAIU it isn't.


📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 530 |-                 t_min1 = -t_min1;
 531 |-                 t_max1 = -t_max1;
 547 |+                 sign = -1;
```

> Username: awulkiew  
> Created_at: 2018-05-10 13:33:36 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187330626  

It's a matter of preference but `int sign = diff1 >= 0 ? 1 : -1;` would be shorter.


📁 include/boost/geometry/algorithms/detail/envelope/segment.hpp

```diff
 335 |         CalculationType alp1, alp2;
 337 |-         strategy.apply(lon1_rad, lat1_rad, lon2_rad, lat2_rad, alp1, alp2);
 336 |+         strategy.apply<true, true>(lon1_rad, lat1_rad, lon2_rad, lat2_rad, alp1, alp2);
```

> Username: awulkiew  
> Created_at: 2018-05-10 13:46:06 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187333946  

I'm surprised that this compiles. Shouldn't it be `strategy.template apply<true, true>(...)`?  
But anyway. What do you think about replacing template parameters with function overloads? E.g.:  
  
    strategy.apply(lon1_rad, lat1_rad, lon2_rad, lat2_rad, alp1, alp2);  
    strategy.apply(lon1_rad, lat1_rad, lon2_rad, lat2_rad, alp1);  
    strategy.apply_reverse(lon1_rad, lat1_rad, lon2_rad, lat2_rad, alp2);  
  
or rather  
  
    T alp1 = strategy.apply(lon1_rad, lat1_rad, lon2_rad, lat2_rad);  
    T alp2 = strategy.apply_reverse(lon1_rad, lat1_rad, lon2_rad, lat2_rad);  
  
or something similar. To make it more clear and to get rid of dummy argument. And in the latter case to encourage initialization of variable at the same line.  
  
Btw, isn't it true that reverse azimuth should be equal to azimuth of segment with switched endpoints?

> Username: vissarion  
> Created_at: 2018-05-11 09:32:33 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187566188  

I prefer the first proposed interface (which I didn't implement before because I wanted to stick on single "apply" name but I agree it is cleaner). The second interface above call two times the azimuth formula when both alph1 and alph2 needed. Regarding switched endpoints it is not always true and that is the reason for this change here. Sometimes you have to subtract/add pi etc but this is formula's job.

> Username: awulkiew  
> Created_at: 2018-05-11 10:00:03 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187572653  

The second interface only presents the two latter functions that are different. The one taking 2 output parameters would also be there. So no double calculation also in this case.


📁 include/boost/geometry/formulas/meridian_segment.hpp

```diff
  55 |+     static bool meridian_not_crossing_pole(T lat1, T lat2, T diff)
  56 |+     {
  57 |+         T half_pi = math::pi<T>()/T(2);
```

> Username: awulkiew  
> Created_at: 2018-05-10 13:48:10 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187334610  

`T const half_pi = math::half_pi<T>();`  
or even  
`static T const half_pi = math::half_pi<T>();`


📁 include/boost/geometry/strategies/cartesian/distance_projected_point.hpp

```diff
 108 |+     {
 109 |+         return Strategy();
 110 |+     }
```

> Username: awulkiew  
> Created_at: 2018-05-10 13:56:57 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187337186  

Strategy is Point-Point distance strategy passed into this Point-Segment distance strategy as template parameter.  
- `get_distance_strategy()` returns Pt-Pt distance strategy right? If that's the case then the name is ambiguous.  
- `get_azimuth_strategy()` should return the correct strategy, if there is no azimuth strategy in cartesian CS, or this strategy is not used, then this probably means that the code is not correctly divided into algorithm (CS-agnostic part) and strategy (CS-specific part) parts.  
- `get_envelope_segment_strategy()` it should be possible to return correct strategy here however the fact that you're ok with returning Pt-Pt distance strategy instead indicates that in cartesian CS this strategy is not used by the algrorithm somehow.  
  
So this either means that the algorithm is dispatched by CS which is not correct or that these methods are required by some specific strategy, i.e. spherical and geographic Seg-Box strategies. I assume the latter is correct and this means that you moved the responsibility for some things to Pt-Segment strategy instead of doing those things in Seg-Box strategy. If azimuth and envelope is needed by a specific Seg-Box strategy it should be handled there, not here.


📁 include/boost/geometry/strategies/cartesian/distance_pythagoras.hpp

```diff
 120 |+ 
 121 |+     template <typename CT>
 122 |+     inline CT vertical_or_meridian(CT const& lat1, CT const& lat2) const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:05:47 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187339809  

Similar story here. There are no meridians in cartesian. It looks like you wanted to move responsibility for some calculations from Seg-Box or Pt-Seg strategy. Are `vertical_or_meridian()` or `coordinate()` needed in distance Pt/Pt algorithm?

---

📁 include/boost/geometry/strategies/cartesian/distance_pythagoras.hpp

```diff
 195 |+ 
 196 |+     template <typename CT>
 197 |+     inline CT vertical_or_meridian(CT const& lat1, CT const& lat2) const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:07:19 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187340281  

Same here, this doesn't belong here.


📁 include/boost/geometry/strategies/cartesian/mirror_box.hpp

```diff
  18 |+ {
  19 |+ 
  20 |+ struct cartesian
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:10:33 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187341259  

What is this strategy? Is there mirror_box() algorithm? It doesn't do anything for cartesian. If there is no algorithm needing it and it doesn't do anything in castesian then my guess is that it's a utility used in some spherical and/or geographic strategy. If that's the case this shouldn't be a separate strategy.

---

📁 include/boost/geometry/strategies/cartesian/mirror_box.hpp

```diff
  35 |+ 
  36 |+ template <>
  37 |+ struct default_strategy<cartesian_tag>
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:16:08 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187343008  

Is it really required to figure our the default strategy for that? Default strategy is created at the highest level of the algorithm when the user doesn't pass the strategy. Here the most high-level would be one of the distance strategies, everything else should be either implemented inside or derived.


📁 include/boost/geometry/strategies/cartesian/segment_below_of_box.hpp

```diff
  51 |+ 
  52 |+ template <typename LessEqual, typename ReturnType, typename PPStrategy>
  53 |+ struct default_strategy<LessEqual, ReturnType, PPStrategy, cartesian_tag>
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:18:58 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187343787  

Same story here. Why is it a separate strategy and why is it possible to get default one for specific CS? Is it going to be used as a standalone strategy? In what scenario?


📁 include/boost/geometry/strategies/geographic/azimuth.hpp

```diff
  58 |+         bool EnableReverseAzimuth,
  59 |+         typename T
  60 |+     >
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:22:44 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187344932  

As mentioned before. I'm not sure that this is a good function design because the user needs to have template arguments and function arguments synchronized. I'd rather implement this as functions taking exactly these output arguments which will be returned. In case of overloads returning single value it could be returned from the function instead of taken as output argument. I.e.:  
  
    strategy.apply(..., azi1, azi2);  
    T azi1 = strategy.apply(...);  
    T azi2 = strategy.apply_reverse(...);

---

📁 include/boost/geometry/strategies/geographic/azimuth.hpp

```diff
  98 |+         apply<true, false>(lon1_rad, lat1_rad,
  99 |+                            lon2_rad, lat2_rad,
 100 |+                            a1, a1);
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:26:55 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187346189  

Right, so there already is `apply()` returning one value.


📁 include/boost/geometry/strategies/geographic/distance.hpp

```diff
 128 |+     template <unsigned int Index, typename Point1, typename Point2>
 129 |+     inline typename calculation_type<Point1, Point2>::type
 130 |+     coordinate(Point1 const& p1, Point2 const& p2) const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:28:58 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187346862  

If this function or the one below is not used by distance Pt-Pt algorithm then this is not a place for this functionality. AFAIU it's required by some upper-level strategy and should be implemented there.

---

📁 include/boost/geometry/strategies/geographic/distance.hpp

```diff
 146 |     template <typename CT>
 130 |-     inline CT meridian(CT lat1, CT lat2) const
 147 |+     inline CT vertical_or_meridian(CT lat1, CT lat2) const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:29:09 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187346914  

Same here.


📁 include/boost/geometry/strategies/geographic/distance_cross_track.hpp

```diff
 128 |+     };
 129 |+ 
 130 |+     inline typename envelope_segment_strategy::type get_envelope_segment_strategy() const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:33:11 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187348132  

Same story if not needed by the algorithm but needed by upper-level strategy then should be implemented there. Unless there is no Seg-Box strategy and this Pt-Seg strategy is passed into distance algorithm for Seg-Box arguments. But then it'd mean that the algorithm has to be dispatched by CS because cartesian Pt-Seg strategy returns dummy strategies here.


📁 include/boost/geometry/strategies/geographic/segment_below_of_box.hpp

```diff
  21 |+ 
  22 |+ template <typename LessEqual, typename ReturnType, typename PPStrategy>
  23 |+ struct geographic
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:36:08 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187349054  

AFAIU this should not be a separate strategy but an internal detail of other strategy.


📁 include/boost/geometry/strategies/spherical/azimuth.hpp

```diff
  43 |+         typename T
  44 |+     >
  45 |+     inline void apply(T const& lon1_rad, T const& lat1_rad,
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:36:54 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187349340  

The interface here as well.


📁 include/boost/geometry/strategies/spherical/distance_cross_track_box_box.hpp

```diff
 164 |                     >::apply(ps_strategy, ps_strategy.get_distance_strategy()
 165 |-                                .meridian(lat_min1, lat_max2));
 165 |+                                .vertical_or_meridian(lat_min1, lat_max2));
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:40:07 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187350308  

Right, this function should be implemented in this strategy or some details, not in P/P strategy. It's neither needed in P/P algorithm nor P/S algorithm. It's only needed here in S/B strategy.


📁 include/boost/geometry/strategies/spherical/distance_cross_track.hpp

```diff
 375 |+     };
 376 |+ 
 377 |+     inline typename envelope_segment_strategy::type get_envelope_segment_strategy() const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:40:37 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187350460  

AFAIU this is not the place for these functions.

---

📁 include/boost/geometry/strategies/spherical/distance_cross_track.hpp

```diff
 587 |+     };
 588 |+ 
 589 |+     inline typename envelope_segment_strategy::type get_envelope_segment_strategy() const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:40:45 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187350517  

AFAIU this is not the place for these functions.


📁 include/boost/geometry/strategies/spherical/distance_cross_track_point_box.hpp

```diff
 103 |-                         >::apply(ps_strategy, ps_strategy.get_distance_strategy().meridian(plat, lat_max));
 103 |+                         >::apply(ps_strategy, ps_strategy.get_distance_strategy()
 104 |+                                  .vertical_or_meridian(plat, lat_max));
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:42:50 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187351220  

Same here.


📁 include/boost/geometry/strategies/spherical/distance_haversine.hpp

```diff
 109 |     template <typename T1, typename T2>
  98 |-     inline radius_type meridian(T1 lat1, T2 lat2) const
 110 |+     inline radius_type vertical_or_meridian(T1 lat1, T2 lat2) const
```

> Username: awulkiew  
> Created_at: 2018-05-10 14:48:17 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r187353109  

Same here. Is this element of the interface requried by the distance P/P algorithm? If it's a CS specific optimization used in some other strategy it should be implemented there, not here.


---

## Comment 2

> Username: vissarion  
> Created_at: 2018-05-17 08:02:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#issuecomment-389780346  

@awulkiew  thanks for the comments.  
  
I created an S/B distance strategy that provides all the functionality needed from the S/B distance algorithm; e.g. it provides the utilities [segment_below_of_box](https://github.com/vissarion/geometry/blob/4a237e6878bc396557d8fe3b8c329c519da34b50/include/boost/geometry/strategies/spherical/distance_segment_box.hpp#L34) and [mirror](https://github.com/vissarion/geometry/blob/4a237e6878bc396557d8fe3b8c329c519da34b50/include/boost/geometry/strategies/spherical/distance_segment_box.hpp#L133).   
  
Vertical_or_meridian method is moved to P/S strategy from P/P. This is the right place since it is used by higher order strategies like P/B and S/B that derive a P/S strategy.  
  
Regarding the interface of strategies I changed it a bit in order to have a unique interface for spherical distance strategies (apart from P/P one) i.e. the number type as a first parameter and a P/P strategy as the second. In general I think it would make things clearer to have an "as unique as possible" interface for all distance strategies but that should be done in a separate PR.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-02 12:15:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/478#pullrequestreview-133600950  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  76 |+         disjoint_info(type t) : t_(t){}
  77 |+         operator type () const {return t_;}
  78 |+         type t_;
```

> Username: awulkiew  
> Created_at: 2018-07-02 12:15:45 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r199476473  

Typically we put `m_` before member variable.

> Username: awulkiew  
> Created_at: 2018-07-04 13:03:27 UTC  
> Updated_at: 2018-07-04 13:03:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r200119945  

I saw that you did this change in merge commit. Not the best way but ok. Btw my point was not to name it `m_` but `m_t` but ok this can be done later.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2018-07-02 12:18:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/478#pullrequestreview-133601798  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  75 |+         };
  76 |+         disjoint_info(type t) : t_(t){}
  77 |+         operator type () const {return t_;}
```

> Username: awulkiew  
> Created_at: 2018-07-02 12:18:37 UTC  
> Updated_at: 2018-07-04 12:55:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#discussion_r199477149  

Why is this class needed if you're converting it to enum? Why not just use enum instead?


---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-07-02 13:15:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/478#issuecomment-401800258  

Thanks! I'm ok with merging afer resolving conflicts.

---
