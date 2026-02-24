# #533 Define CS-specific behavior of algorithms only with strategy (part 1). [Merged]

> Username: awulkiew  
> Created at: 2018-11-29 17:52:57 UTC  
> Updated at: 2018-12-16 21:24:34 UTC  
> Merged at: 2018-12-16 21:24:33 UTC  
> Closed at: 2018-12-16 21:24:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/533  

In Boost.Geometry if a strategy is passed into the algorithm the coordinate system of geometries are ignored. The CS-specific behavior of a strategy is used. At least this is the case at the "highest-level". Internally still in many places the information about the CS defined by strategy is not propagated, default strategies are created from geometry types and algorithms dispatched by CS in algorithms instead of in strategies. To fix this:  
- proper strategies has to be "derived" from "higher-level" strategy and propagated down to each underlying algorithm,  
- no default strategy should be created internally,  
- to be in line with the library design all CS specific code (dispatched by CS) should be moved to strategies directory.  
  
This PR is the first part of changes addressing the above problems. I decided to propose it because it is big enough already and I want to know if the direction I'm moving in is correct.  
  
In order to find places where change is needed semi-automatically I added new coordinate system `cs::undefined` for which default strategy is not defined nor any algorithm dispatched (besides places where some CS is assumed by default which has to be found manually). Next I modified the following:  
- move CS-specific normalization utility to strategies `namespace strategy::normalize`  
- move CS-specific parts of `expand` and `envelope` to strategies, replacing algorithm dispatched by CS with proper strategies,  
- make `equals`/`disjoint` for P/P clean, i.e. strategy now contains CS-specific code and algorithm calls this strategy (internal utility `equals_point_point` and `disjoint_point_point` requires strategy now). Also create CS-specific ones. Keep the agnostic one using one of the CS specific default strategies based on geometries.  
- move CS-specific parts of `disjoint` for Box/Box into strategies, create proper strategy etc.  
- move CS-specific parts of `disjoint` for Segment/Box into strategies.  
- move CS-specific parts of `within` Point/Box strategy from agnostic strategies to CS-specific, keep agnostic strategy using default CS-specific strategy based on CS defined by geometries.  
- `point_on_border` `Midpoint` parameter is removed because it was cartesian-only and only was hiding problems (if there was a problem in algorithm and `Midpoint` used then one could have create rings touching at midpoint and get the same result as in the case of rings touching at endpoint but without `Midpoint`)  
- `wkt` now internally creates default `disjoint` strategy, I was reluctant to also add `Strategy` argument to `wkt` because I thought that stream manipulators should have only 1 argument but `dsv` has more. Still passing `disjoint` strategy to `wkt` feels wierd.  
- azimuth, normalize, expand, envelope, disjoint, etc. strategies are derived from "higher-level" strategies and passed internally.  
- overlapping interiors condition fixed in `is_valid`.  
- simplify within concept check parameters  
  
The removal of `Midpoint` in `point_on_border` exposed a bug in `is_valid` erroreously returning a false-positive reported here: https://github.com/boostorg/geometry/issues/515. So at this point several `sym_difference` tests fail, which is correct.  
  
This work is also why I was postponing fixing https://github.com/boostorg/geometry/issues/466. I'm going bottom-up with the changes and only now approaching the level of envelope for Polygon. Fixing this issue requires to have a different algorithm for cartesian and non-cartesian Rings and Single-Geometries contained in Multi-Geometries. So at least parts of the algorithm would have to be moved to strategies.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2018-12-12 09:35:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/533#pullrequestreview-184071403  

Thanks a lot. I went to most of it and it looks consistent and a lot cleaner. Looks like a lot of code could be deleted.  
I'm OK with merging. We both touched a little bit of the same areas so I don't hope that there are too many conflicts. As far as I could judge, mostly the touched lines are different so I think there are not many.  
  
But... is it OK if I merge first?

📁 include/boost/geometry/algorithms/detail/disjoint/multipoint_geometry.hpp

```diff
 324 |         static inline bool apply(Box const& box, Point const& point)
 325 |         {
 326 |             // The default strategy is enough for Point/Box
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:08:23 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240929626  

Maybe this comment can be removed now (is it still the default strategy? It can now be specified)

> Username: awulkiew  
> Created_at: 2018-12-13 02:02:27 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241250675  

Yes, it should have been removed.


📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 137 |-             swap(lon1, lat1, lon2, lat2);
 151 |+             std::swap(lon1, lon2);
 152 |+             std::swap(lat1, lat2);
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:11:46 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240930669  

This is more clear indeed


📁 include/boost/geometry/algorithms/detail/envelope/range.hpp

```diff
  81 |     {
  83 |-         return apply(boost::begin(range), boost::end(range), mbr, strategy);
  82 |+         return apply(Strategy::begin(range), Strategy::end(range), mbr, strategy);
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:14:41 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240931631  

This is surprising. What is this for kind of strategy? So it should implement range like features?

> Username: awulkiew  
> Created_at: 2018-12-13 02:06:52 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241251239  

The problem with envelope is that in cartesian we can process points but in spherical and geographic we have to analyse segments because geodesic's vertex may have different latitude (higher or lower on northern or southern hemisphere respectively) than the endpoints. So the strategy feeds the algorithm either with points or with segments. Previously the dispatching by CS was done in the algorithm. I decided to do it this way to allow the strategy to define the smallest Cs-specific portions of the algorithm. Otherwise I'd have to implement the whole calculation of envelope of a range as strategy.

> Username: vissarion  
> Created_at: 2018-12-13 14:05:08 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241410638  

+1


📁 include/boost/geometry/io/wkt/read.hpp

```diff
 309 |+     static inline bool disjoint(point_type const& p1, point_type const& p2)
 310 |+     {
 311 |+         // TODO: pass strategy
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:22:32 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240934461  

OK for me, PR is large enough

> Username: awulkiew  
> Created_at: 2018-12-13 02:09:01 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241251563  

Yes, I wanted to postpone it because I didn't want to force the user to pass within point strategy into `read_wkt()`. We should probably have separate wkt strategy for that.


📁 include/boost/geometry/core/cs.hpp

```diff
  73 | template <>
  74 |- struct coordinate_system_units<geometry::radian>
  74 |+ struct define_angular_units<geometry::radian>
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:26:23 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240935785  

Right, I see. We don't get backward compatibility issues with this?  
  
OK, I see that it is part of core_detail - then it should be no problem

---

📁 include/boost/geometry/core/cs.hpp

```diff
 115 |- };
 110 |+     : core_detail::define_angular_units<DegreeOrRadian>
 111 |+ {};
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:28:23 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240936504  

What is the reason to derive instead of defining it inside?

> Username: awulkiew  
> Created_at: 2018-12-13 02:11:22 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241251918  

Less lines of code. AFAIU it's a typical way of defining MPL-like meta-functions.

---

📁 include/boost/geometry/core/cs.hpp

```diff
 171 |+ \ingroup cs
 172 |+ */
 173 |+ struct undefined {};
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:28:56 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240936715  

OK


📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 334 |+         typename IntersectionStrategy, typename RobustPolicy
 335 |+     >
 336 |     static inline void advance_to_non_duplicate_next(Iterator& next,
```

> Username: barendgehrels  
> Created_at: 2018-12-12 09:32:01 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r240937739  

I moved this. So this will give a merge conflict later.

> Username: awulkiew  
> Created_at: 2018-12-13 02:11:57 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241251991  

No problem, I'll resolve them.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2018-12-13 02:12:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#issuecomment-446816893  

No problem with merging your PR first.

---

## Review 3 [Commented]

> Username: vissarion  
> Created_at: 2018-12-13 14:44:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/533#pullrequestreview-184666343  

Thanks Adam, it makes more sense now and much clearer. I reviewed it and added some minor comments. In general I am ok with merging and looking forward to part II.   
  
Additionally, since it seems the right thread to ask: if strategies define the CS-specific computation of an algorithm what is an agnostic strategy? Should just be part of the corresponding algorithm?

📁 include/boost/geometry/algorithms/area.hpp

```diff
 229 | 
 230 |+ template <>
 231 |+ struct area<default_strategy>
```

> Username: vissarion  
> Created_at: 2018-12-13 13:46:00 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241404143  

This is indeed much better. Are going to propose it for the resolve_strategy of all algorithms?

> Username: awulkiew  
> Created_at: 2018-12-13 16:57:38 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241481946  

No, this was done from technical reasons. In order to instantiate `area_result` with correct arguments and because it's a return type of member function I had to do it this way. Otherwise I'd left it as it was.


📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
  53 | template <typename CS_Tag>
  54 | struct disjoint_segment_box_sphere_or_spheroid
```

> Username: vissarion  
> Created_at: 2018-12-13 13:49:24 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241405184  

This should also change since it is CS-specific, right?

> Username: awulkiew  
> Created_at: 2018-12-13 17:00:45 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241483214  

Yes, technically this should probably be moved to strategies. I either left it here to limit the amount of changes or I simply missed it. It's also probable that this code is still used in some other place too. I'd have to check. If possible I'll move it to strategies.


📁 include/boost/geometry/algorithms/detail/envelope/interface.hpp

```diff
  62 |                              Box& box,
```

> Username: vissarion  
> Created_at: 2018-12-13 14:00:32 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241409106  

why do not split here in two structs and dispatch as in area and distance?

> Username: awulkiew  
> Created_at: 2018-12-13 17:04:49 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241484676  

Because here it's sufficient to distinguish between strategies on function level, the compiler can pick the correct function automatically. With area and distance the function return type was based on strategy and this caused problems. Even the `Geometry` parameter could probably be moved.


📁 include/boost/geometry/algorithms/detail/envelope/range.hpp

```diff
  72 |+                     <
  73 |+                         Box, value_type
  74 |+                     >::apply(mbr, *it, strategy.get_element_expand_strategy());
```

> Username: vissarion  
> Created_at: 2018-12-13 14:04:24 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241410393  

so, `strategy` here is a "envelope_range" strategy that contains an expand strategy for elements. Is this a bit unexpected ? I mean to hide an expand strategy in an envelope strategy.

> Username: awulkiew  
> Created_at: 2018-12-13 17:17:07 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241489068  

Yes, expand and envelope implementation is tightly bound together. The envelope range has to know how to extract the simple geometries from a range (points in cartesian and segments in spherical/geographic) then how to calculate the bounding boxes from them (e.g. spherical/geographic segment to box) and how to expand by them (e.g. spherical/geographic segment to spherical box and then expansion of spherical box). There are such dependencies in envelope/expand code where one strategy needs lower-level strategies of the other algorithm and this propagates "up". AFAIR I haven't yet refactor the envelope code for Multi-Geometries which adds another layer of dependencies. I left the dependencies as they were before because I wanted to avoid dividing the whole algorithms into strategies one for each kind of Geometry (one for each simple geometry, next for all ranges and next for multi-ranges) but maybe this is how it should be done. But I'd prefer to do it in part 2.

> Username: vissarion  
> Created_at: 2018-12-14 11:23:52 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241723476  

Sure, better in part 2.


📁 include/boost/geometry/algorithms/detail/equals/point_point.hpp

```diff
  39 | {
  42 |-     return ! detail::disjoint::disjoint_point_point(point1, point2);
  40 |+     return Strategy::apply(point1, point2);
```

> Username: vissarion  
> Created_at: 2018-12-13 14:10:07 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241412317  

Why we need a special strategy for checking if 2 points are equal? Is it the same for all CS? Unless if you consider normalization, but in this case how normalization strategy is passes here? What am I missing...

> Username: awulkiew  
> Created_at: 2018-12-13 17:24:32 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241491527  

In general yes, this could be done the same for all coordinate systems. For this we'd have to agree what's the valid range of coordinates, e.g. if `lon_deg = -180+eps/2` is valid, since this would be true: `bg::math::equals(lon_deg + 360, 180)`, this coordinate would be ambiguous and cartesian-like comparison would fail. Currently we have this normalization in place which even if the user represents the coordinates ambiguously (`-180` vs `180` or even as something outside valid range) the relation operations still works. In order for this to work we need separate strategies for various CSes even for points.

> Username: vissarion  
> Created_at: 2018-12-14 11:55:03 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241731061  

I do not have a strong opinion here, since I cannot predict the user needs, but what about fixing a range e.g. `(-180,180]`, then if the user compare invalid coordinates the result is "unexpected" as with invalid geometries. In any case the user could be use normalization before comparing and have an always correct result.

> Username: vissarion  
> Created_at: 2018-12-14 11:56:38 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241731559  

In any case, this should be done in a new PR.

> Username: awulkiew  
> Created_at: 2018-12-14 13:07:44 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241748591  

Yes, the normalization could also be a part of `correct`. However I think that assuming that -180 is correct is common practice and if we changed the existing behavior users could get unexpected results.


📁 include/boost/geometry/algorithms/detail/expand/interface.hpp

```diff
  60 |                              Geometry const& geometry,
```

> Username: vissarion  
> Created_at: 2018-12-13 14:12:05 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241412988  

same here

> Username: awulkiew  
> Created_at: 2018-12-13 17:27:34 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241492498  

Right, here it's also sufficient to do this on a function-level vs struct level. This means less template arguments passed explicitly by us because the compiler can automatically instantiate the correct function overload.


📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 492 | 
 493 |         typename IntersectionStrategy::envelope_strategy_type const
 494 |             envelope_strategy = intersection_strategy.get_envelope_strategy();
```

> Username: vissarion  
> Created_at: 2018-12-13 14:28:45 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241418952  

is it needed for intersection strategy to contain both envelope and expand strategies? Why not passing them independently as in the strategies in `disjoint_segment_box`?

> Username: awulkiew  
> Created_at: 2018-12-13 17:29:51 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241493235  

The `sectionalize` uses 2 policies. And even if we wanted to calculate boxes and expand spherical boxes by other boxes instead of segments, spherical boxes are expanded in different way than cartesian ones so we'd also need second strategy here (expand box/box).


📁 include/boost/geometry/algorithms/detail/within/interface.hpp

```diff
  70 |                              Geometry2 const& geometry2,
```

> Username: vissarion  
> Created_at: 2018-12-13 14:30:52 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241419795  

same here. why no dispatching as in area and distance?

> Username: awulkiew  
> Created_at: 2018-12-16 14:40:45 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241983219  

I answered that before but also here, dispatching based on function templates doesn't force us to explicitly state the types. I've done it there only because I was forced to do it, not because I wanted to do it. ;)


📁 test/strategies/segment_envelope.cpp

```diff
   1 | // Boost.Geometry
```

> Username: vissarion  
> Created_at: 2018-12-13 14:41:19 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241423842  

now there is strategy named `envelope_segment` the name of this file should also change

> Username: awulkiew  
> Created_at: 2018-12-16 14:40:56 UTC  
> Updated_at: 2018-12-16 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#discussion_r241983224  

Will do.


---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-12-16 14:34:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/533#issuecomment-447647950  

Merged develop with recently merged https://github.com/boostorg/geometry/pull/531 and https://github.com/boostorg/geometry/pull/540.  
  
As a reminder, note that some `sym_difference` tests fails due to this: https://github.com/boostorg/geometry/issues/515  
  
> Additionally, since it seems the right thread to ask: if strategies define the CS-specific computation of an algorithm what is an agnostic strategy? Should just be part of the corresponding algorithm?  
  
@vissarion I'm not sure. Maybe it indeed should simply be a different algorithm with distinguished name, like `bg::algorithm_variant1`, `bg::algorithm_variant2`, `bg::algorithm_variant3` and the default `bg::algorithm`. In that case I'm not sure if the name "Strategy" is a good name since it's more general, maybe it would then be "CSStrategy". But anyway, I'm not sure if we can change that now because of causing problems to the users. Thought If there is only 1 agnostic strategy for algorithm I doubt anyone is using it explicitly. Maybe besides buffer's strategies which are probably used heavily because they are something between Strategies and Parameters. And on top of this we might decide to move to C++11 like some other libraries and this could be a good opportunity to redefine some aspects of the library. IF we want to talk about all of it I suggest to create an Issue.

---
