# #923 New algorithm closest points with new strategies [Merged]

> Username: vissarion  
> Created at: 2021-10-11 14:22:24 UTC  
> Updated at: 2021-12-01 14:50:49 UTC  
> Merged at: 2021-12-01 14:50:44 UTC  
> Closed at: 2021-12-01 14:50:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/923  

This PR implements the closest points algorithm of PR https://github.com/boostorg/geometry/pull/707 with new umbrella strategies.   
  
It contains only the cases of cartesian point - geometry (no box) to keep it small. The rest of the functionality will follow in new PRs. At the end of this process I will close https://github.com/boostorg/geometry/pull/707

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:11:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776762127  

📁 include/boost/geometry/strategies/closest_points/cartesian.hpp

```diff
  47 |+                                distance::detail::enable_if_pp_t<Geometry1, Geometry2> * = nullptr)
  48 |+     {
  49 |+         return strategy::closest_points::dummy_pt_pt();
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:11:48 UTC  
> Updated_at: 2021-10-11 23:11:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726627106  

What is the reasoning behind this? Shouldn't this depend on the CS?

> Username: awulkiew  
> Created_at: 2021-10-12 00:14:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726670399  

Ah I understand, this is not used at all because points define the segment. So this entire function can be removed I think.

> Username: vissarion  
> Created_at: 2021-10-12 08:47:51 UTC  
> Updated_at: 2021-10-12 08:47:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726901084  

this is used in dispatch https://github.com/boostorg/geometry/pull/923/files#diff-dff96d044895b1ba497bfd0d8be58366730c176f90d4168118111e6a48064641R43

> Username: awulkiew  
> Created_at: 2021-10-12 08:52:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726905353  

Right. Though `closest_points` doesn't need to be dispatched by `StrategyTag`.

> Username: vissarion  
> Created_at: 2021-10-12 09:24:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726933015  

but if I remove legacy strategies, this is not needed for sure


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:15:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776763522  

📁 include/boost/geometry/strategies/closest_points/cartesian.hpp

```diff
  99 |+         return strategy::distance::projected_point<CalculationType,
 100 |+             strategy::distance::comparable::pythagoras<CalculationType>>();
 101 |+     }
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:15:34 UTC  
> Updated_at: 2021-10-11 23:18:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726630121  

These getters should be named `distance` and return regular distance strategies. Then this umbrella strategy should be passed into `comparable_distance()` algorithm. Alternatively if you want to call legacy comparable strategy explicitly you could get legacy `distance` strategy from here and convert it to legacy `comparable_distance` strategy with `strategy::distance::services::get_comparable`, e.g. like that:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/distance/comparable.hpp#L43-L46

> Username: vissarion  
> Created_at: 2021-10-12 11:12:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727018387  

these are passed to `closest_feature::geometry_to_range` structures so the alternative is the option I implemented. But it is not clear to me why not returning the `comparable_distance` directly without conversion, since closest_points only need the comparable distances.

> Username: awulkiew  
> Created_at: 2021-10-12 11:37:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727047443  

Because comparable strategies can be retrieved from distance strategies so they are redundant. This way any umbrella strategy can define only distance strategy and any algorithm that needs comparable strategy can convert it itself if needed. This is what `comparable_distance()` algorithm does. This way it's not needed to define both `distance()` and `comparable_distance()` getters in umbrella strategies. Note that right now you'd have to add `comparable_distance()` getter to `strategies::cartesian` umbrella strategy in order to allow passing it to `closest_points`. It already defines `distance()` getter which can be used to get comparable strategy too. This is what `strategy::distance::services::get_comparable` is for.

> Username: awulkiew  
> Created_at: 2021-10-12 11:42:23 UTC  
> Updated_at: 2021-10-12 11:46:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727053516  

Btw, the fact that `comparable_distance()` algorithm requires umbrella strategy with `distance()` getter also means that you can simply pass the umbrella strategy to `comparable_distance()` and this will just work. There is no need to get legacy strategy. That is, unless you're calling some internal distance utilities (I don't remember but I guess that legacy strategies may be required there).

> Username: awulkiew  
> Created_at: 2021-10-12 11:50:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727059596  

There is one more positive aspect which is that you could derive `closest_points` umbrella strategy from `distance` umbrella strategy instead of `relate` and all of the `distance()` getters would be already defined.

> Username: vissarion  
> Created_at: 2021-10-12 12:48:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727103397  

> There is one more positive aspect which is that you could derive closest_points umbrella strategy from distance umbrella strategy instead of relate and all of the distance() getters would be already defined.  
  
I haven't noticed that, cool. What's the reason of distance inheriting relate strategies? They seem semantically unrelated; distance is metric while relate computes some combinatorial (non-metric) information.

> Username: awulkiew  
> Created_at: 2021-10-12 17:30:26 UTC  
> Updated_at: 2021-10-12 17:32:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727352206  

Relational operations may be called in `distance()` to check if the algorithm should finish and return `0` right away, e.g.:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/distance/point_to_geometry.hpp#L228  
or to check some other things.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:22:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776765901  

📁 include/boost/geometry/strategies/closest_points/services.hpp

```diff
  71 |+         "Not implemented for this Strategy.",
  72 |+         Strategy);
  73 |+ };
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:22:12 UTC  
> Updated_at: 2021-10-11 23:25:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726634966  

Are these needed? `strategy_converter` is there to ensure backward compatibility when legacy strategy is passed. This is not a problem here since this is a new algorithm and will probably require umbrella strategy from the start. `custom_strategy_converter` is distance-specific and for backward compatibility as well. `tag` is distance-specific too and is used to allow the user to pass different kinds of strategies for the same input geometries. Furthermore it's used for legacy strategies, not umbrella strategies. This is probably not going to be used in `closed_points`.

> Username: vissarion  
> Created_at: 2021-10-12 09:25:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726933728  

I agree, I will remove legacy strategies


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:27:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776767656  

📁 include/boost/geometry/strategies/closest_points.hpp

```diff
  30 |+         "Not implemented for this Strategy.",
  31 |+         Strategy);
  32 |+ };
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:27:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726638381  

Is this really needed here? Will the algorithm be dispatched by the strategy too as it is in case of `distance`?

> Username: vissarion  
> Created_at: 2021-10-12 09:25:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726933966  

I agree, I will remove legacy strategies and this will be removed


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:30:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776768983  

📁 include/boost/geometry/strategies/closest_points.hpp

```diff
  62 |+         "Not implemented for this Point type combination.",
  63 |+         Point1, Point2, CsTag1, CsTag2);
  64 |+ };
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:30:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726641259  

Do we need to implement legacy `default_strategy` for new algorithms? This is not going to be used anywhere since the algorithm will create default umbrella strategy insttead. These are left for older strategies in case users had them in their code but in general they are not needed.

> Username: vissarion  
> Created_at: 2021-10-12 09:25:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726934072  

I agree, I will remove legacy strategies and this will be removed


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:35:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776770703  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
 345 |+     <
 346 |+         P1, P2, Strategy, point_tag, point_tag,
 347 |+         strategy_tag_distance_point_point, false
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:35:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726644800  

Right, so the algorithm doesn't have to be dispatched by `StrategyTag` like `distance`. We can require one, specific kind of strategy for a pair of geometries. Unless you plan to support various kinds of strategies for the same input?

> Username: vissarion  
> Created_at: 2021-10-12 09:26:55 UTC  
> Updated_at: 2021-10-12 09:26:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726934890  

No, I agree, one strategy for the same input is ok


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:44:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776773951  

📁 include/boost/geometry/strategies/closest_points/services.hpp

```diff
  48 |+     {
  49 |+         return strategies::detail::not_implemented();
  50 |+     }
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:44:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726652199  

I think that simple static assertion would be enough. I used this in `distance` for backward compatibility.

> Username: vissarion  
> Created_at: 2021-10-12 09:31:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726938256  

the getter is not needed, so I removed it


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:46:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776774542  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
 157 |+         >::apply(geometry1, geometry2, shortest_seg, converter::get(strategy));
 158 |+     }
 159 |+ };
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:46:40 UTC  
> Updated_at: 2021-10-11 23:57:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726653481  

All of this is probably not needed unless we want to allow users to pass legacy-type strategies instead of umbrella strategies. All of them are new so I think it's just fine to require umbrella strategies.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:50:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776775844  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
 372 |+ };
 373 |+ */
 374 |+ } // namespace resolve_variant
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:50:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726654890  

Right, there is no need to use the old approach. The new one is proposed here: https://github.com/boostorg/geometry/pull/922


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:52:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776776521  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
 409 |+     concepts::check<Geometry2 const>();
 410 |+ 
 411 |+     geometry::clear(shortest_seg);
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:52:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726655970  

`clear` does nothing for segments:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/clear.hpp#L109-L112  
Or is this a two-point Linestring?

> Username: vissarion  
> Created_at: 2021-10-12 09:41:12 UTC  
> Updated_at: 2021-10-12 09:41:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726946480  

I see, on the other hand I do not see a need of clearing the output segment here, hence I removed that command.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:54:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776777127  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
  18 |+ #include <boost/geometry/algorithms/dispatch/closest_points.hpp>
  19 |+ 
  20 |+ #include <boost/geometry/algorithms/distance.hpp>
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:54:32 UTC  
> Updated_at: 2021-10-11 23:56:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726657337  

This also includes the implementation. You should probably include only the interface here and the implementation bits of `distance` in the `implementation.hpp`.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-11 23:57:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776778073  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
  84 |+ <
  85 |+     typename Strategy,
  86 |+     bool IsUmbrella = strategies::detail::is_umbrella_strategy<Strategy>::value
```

> Username: awulkiew  
> Created_at: 2021-10-11 23:57:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726659620  

If we decided to require only umbrella strategies in this new algorithm this wouldn't be needed.


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 00:01:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776779392  

📁 include/boost/geometry/algorithms/detail/closest_points/utilities.hpp

```diff
  27 |+         set<0,1>(segment, get<1>(p1));
  28 |+         set<1,0>(segment, get<0>(p2));
  29 |+         set<1,1>(segment, get<1>(p2));
```

> Username: awulkiew  
> Created_at: 2021-10-12 00:01:15 UTC  
> Updated_at: 2021-10-12 00:05:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726662746  

You could use n-dimensional [detail::assign_point_to_index](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/assign_indexed_point.hpp#L55).

> Username: awulkiew  
> Created_at: 2021-10-12 00:02:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726663543  

Or even replace this utility entirely with 2 calls.

> Username: vissarion  
> Created_at: 2021-10-12 09:56:24 UTC  
> Updated_at: 2021-10-12 09:56:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726958515  

I prefer to keep it since it is called in many places.


---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 00:04:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776780572  

📁 include/boost/geometry/algorithms/detail/closest_points/utilities.hpp

```diff
  42 |+         temp = get<0,1>(segment);
  43 |+         set<0,1>(segment, get<1,1>(segment));
  44 |+         set<1,1>(segment, temp);
```

> Username: awulkiew  
> Created_at: 2021-10-12 00:04:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726665288  

You could use [detail::for_each_dimension](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/algorithm.hpp#L99).


---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 00:08:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776782052  

📁 include/boost/geometry/algorithms/detail/closest_points/utilities.hpp

```diff
  48 |+ 
  49 |+ template <typename Geometry1, typename Geometry2, typename Strategies>
  50 |+ using strategy_t = decltype(
```

> Username: awulkiew  
> Created_at: 2021-10-12 00:08:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726668464  

This name could be confusing. I'd rather assume that `detail::closest_points::strategy_t` is `closest_points` strategy, not `distance` strategy.

> Username: vissarion  
> Created_at: 2021-10-12 11:17:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727024269  

renamed to `distance_strategy_t`


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 00:10:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776782650  

📁 include/boost/geometry/algorithms/dispatch/closest_points.hpp

```diff
  76 |+         <
  77 |+             typename closest_points_strategy_type<Geometry1, Geometry2, Strategy>::type
  78 |+         >::type,
```

> Username: awulkiew  
> Created_at: 2021-10-12 00:10:07 UTC  
> Updated_at: 2021-10-12 00:10:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726669014  

This is probably not needed.


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-12 00:12:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-776783409  

📁 include/boost/geometry/algorithms/dispatch/closest_points.hpp

```diff
  47 |+ struct closest_points_strategy_type<Geometry1, Geometry2, Strategies, true, true>
  48 |+     : closest_points_strategy_type<Geometry2, Geometry1, Strategies, true, false>
  49 |+ {};
```

> Username: awulkiew  
> Created_at: 2021-10-12 00:12:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r726669649  

This is not needed because `closest_points` strategies unlike `distance` strategies doesn't have tags. So there is no need to get legacy strategy here to dispatch the algorithm with it.


---

## Comment 18

> Username: vissarion  
> Created_at: 2021-10-12 11:34:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#issuecomment-940925247  

Thanks for the detailed review. I addressed all the comments. The main change is that closest_points will not support legacy strategies. I think this is OK for new algorithms @barendgehrels what do you think?

---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:19:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778432684  

📁 include/boost/geometry/algorithms/closest_points.hpp

```diff
  14 |+ #include <boost/geometry/algorithms/detail/closest_points/implementation.hpp>
  15 |+ 
  16 |+ #endif // BOOST_GEOMETRY_ALGORITHMS_CLOSEST_POINTS_HPP
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:19:30 UTC  
> Updated_at: 2021-10-13 11:19:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727957326  

No newline. ;)


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:21:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778434191  

📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
  45 |+     typename Geometry1, 
  46 |+     typename Geometry2, 
  47 |+     typename Strategy,
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:21:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727958430  

Since `StrategyTag` is removed `Strategy` parameter is not needed and can be moved to `apply()` member function template.


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:24:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778437265  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
  54 |+ <
  55 |+     typename P1, typename P2, typename Strategies,
  56 |+     bool IsUmbrella = strategies::detail::is_umbrella_strategy<Strategies>::value
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:24:19 UTC  
> Updated_at: 2021-10-13 11:25:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727960724  

This algorithm is not dispatched by `IsUmbrella` right? If that's the case then this parameter is not needed. Also all other parameters can be moved to `apply()` so you don't have to specify them below in the specialization of `closest_points`.


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:25:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778438430  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
  69 |+ <
  70 |+     typename Point, typename Segment, typename Strategies,
  71 |+     bool IsUmbrella = strategies::detail::is_umbrella_strategy<Strategies>::value
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:25:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727961512  

Same here.


---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:27:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778440587  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
 112 |+     typename Range,
 113 |+     closure_selector Closure,
 114 |+     typename Strategies
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:27:54 UTC  
> Updated_at: 2021-10-13 11:28:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727963104  

Here only the `Closure` is needed. And the rest could be moved to `apply()`.


---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:28:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778441569  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
 121 |+         <
 122 |+             Point, Range, Closure
 123 |+         >;
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:28:57 UTC  
> Updated_at: 2021-10-14 17:19:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727963842  

After moving template parameters from class to member function `apply()` this would have to be moved too.


---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:30:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778442871  

📁 include/boost/geometry/algorithms/detail/closest_points/utilities.hpp

```diff
  62 |+ }} // namespace boost::geometry
  63 |+ 
  64 |+ #endif //BOOST_GEOMETRY_ALGORITHMS_DETAIL_CLOSEST_POINTS_UTILITIES_HPP
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:30:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727964843  

No newline.


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:30:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778443264  

📁 include/boost/geometry/algorithms/dispatch/closest_points.hpp

```diff
  38 |+         <
  39 |+             Geometry1, Geometry2
  40 |+         >::type,
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:30:40 UTC  
> Updated_at: 2021-10-13 11:30:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727965179  

This parameter is not needed now.


---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:35:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778447400  

📁 include/boost/geometry/strategies/cartesian/closest_points_pt_seg.hpp

```diff
  51 |+     template <typename Point, typename PointOfSegment>
  52 |+     inline auto
  53 |+     apply(Point const& p, PointOfSegment const& p1, PointOfSegment const& p2) const
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:35:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727968213  

The implementation is nearly the same as in [distance::projected_point](https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/cartesian/distance_projected_point.hpp). We should try doing it once either moving it to e.g. `strategy_detail` and then calling from both of these strategies or implementing it in one of these strategies in a way allowing to be used in both and e.g. deriving one from the other.

> Username: vissarion  
> Created_at: 2021-10-14 15:01:34 UTC  
> Updated_at: 2021-10-15 13:25:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r729075198  

I had it in TODO, but I agree that should be addressed now in this PR, see https://github.com/boostorg/geometry/pull/923/commits/66893926b97e9dd5ba9c03d4025fef2b397fa98d  
EDIT: and the fix in https://github.com/boostorg/geometry/pull/923/commits/5f730458de6c5485d58f427f2208ba87e0e3da4b


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:35:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778447884  

📁 test/algorithms/closest_points/Jamfile.v2

```diff
  14 |+     [ run pl_l.cpp        : : : : algorithms_closest_points_pl_l ]
  15 |+     [ run pl_pl.cpp       : : : : algorithms_closest_points_pl_pl ]
  16 |+     ;
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:35:38 UTC  
> Updated_at: 2021-10-13 11:35:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727968560  

No newline.


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:38:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778450738  

📁 test/algorithms/closest_points/pl_l.cpp

```diff
 401 |+ }
 402 |+ 
 403 |+ */
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:38:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727970726  

No newline.


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:39:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778451145  

📁 test/algorithms/closest_points/pl_pl.cpp

```diff
 155 |+     //test_all_pl_pl<geo_point>(thomas_pp());
 156 |+     //test_all_pl_pl<geo_point>(vincenty_pp());
 157 |+ }
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:39:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727971032  

No newline.


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-13 11:43:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-778455093  

📁 include/boost/geometry/algorithms/detail/closest_points/utilities.hpp

```diff
  56 |+         typename point_type<Geometry1>::type,
  57 |+         typename point_type<Geometry2>::type
  58 |+     >::type;
```

> Username: awulkiew  
> Created_at: 2021-10-13 11:43:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r727973946  

Techniclaly this is going to be the return type of the `distance` strategy, not `comparable_distance` strategy. To get the latter you have to use `get_comparable`. But I think they should be the same in all cases.


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2021-10-17 18:37:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-781478032  

📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
  55 |+     template <typename P1, typename P2, typename Segment, typename Strategies>
  56 |+     static inline void apply(P1 const& p1, P2 const& p2,  
  57 |+                              Segment& shortest_seg, Strategies const& strategies)
```

> Username: awulkiew  
> Created_at: 2021-10-17 18:37:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r730457742  

warning: unused variable.


---

## Comment 33

> Username: vissarion  
> Created_at: 2021-10-20 08:25:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#issuecomment-947441908  

@barendgehrels are you OK with merging this?

---

## Review 34 [Changes requested]

> Username: barendgehrels  
> Created_at: 2021-11-03 11:23:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/923#pullrequestreview-796290978  

Cool that we have this now.  
I think some of the docs are not in this PR, could you add them?  
I added a few comments, can you address them if you agree?  
What is the relation from `closest_points` to `closest_feature` - do we need to keep both?

📁 include/boost/geometry/algorithms/detail/closest_points/implementation.hpp

```diff
  21 |+ //#include <boost/geometry/algorithms/detail/closest_points/segment_to_segment.hpp>
  22 |+ //#include <boost/geometry/algorithms/detail/closest_points/segment_to_box.hpp>
  23 |+ //#include <boost/geometry/algorithms/detail/closest_points/box_to_box.hpp>
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:41:07 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741752647  

Sorry for the late review.  
Can this commented code be removed?

> Username: vissarion  
> Created_at: 2021-11-03 11:43:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741856489  

I will uncomment/implement those in the next PR(s). It is just convenient for me to leave it there, but if you insist I will remove it.

> Username: barendgehrels  
> Created_at: 2021-11-03 11:58:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741866599  

Processing it in next PR is fine for me.

---

📁 include/boost/geometry/algorithms/detail/closest_points/implementation.hpp

```diff
  11 |+ #define BOOST_GEOMETRY_ALGORITHMS_DETAIL_CLOSEST_POINTS_IMPLEMENTATION_HPP
  12 |+ 
  13 |+ // the implementation details
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:41:22 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741752843  

This comment can probably go.


📁 include/boost/geometry/algorithms/detail/closest_points/interface.hpp

```diff
 200 |+ [heading Example]
 201 |+ [closest_points]
 202 |+ [closest_points_output]
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:45:03 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741755787  

These examples seem not yet included in this PR.  
  
- [ ] Add doc/examples

> Username: vissarion  
> Created_at: 2021-11-03 13:20:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741928589  

If this is not a problem, I would like to add the example at the end when the full functionality is implemented.


📁 include/boost/geometry/strategies/cartesian/closest_points_pt_seg.hpp

```diff
  84 |+ 
  85 |+         multiply_value(v, b);
  86 |+         add_point(projected, v);
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:48:34 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741758771  

~~This all looks like copied from `distance_projected_point_ax.hpp`~~  
Oh wait, it's shared with `distance_projected_point.hpp` already, thanks.  
  
(We should once solve the other copy too)


📁 include/boost/geometry/strategies/closest_points/cartesian.hpp

```diff
  73 |+         return strategy::distance::pythagoras_box_box<CalculationType>();
  74 |+     }
  75 |+ */
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:51:17 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741762087  

Can this commented code be removed?


📁 test/algorithms/closest_points/common.hpp

```diff
  61 |+ static inline Segment swap(Segment const& s)
  62 |+ {
  63 |+     Segment s_swaped;
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:52:51 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741763923  

Typo, `swapped` (and we probably don't need the `s_` prefix)

---

📁 test/algorithms/closest_points/common.hpp

```diff
 146 |+     {
 147 |+         //TODO: enable
 148 |+         // swap input geometries and expected segment
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:53:36 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741764759  

Should we do that here?

> Username: vissarion  
> Created_at: 2021-11-03 14:14:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741981865  

I am not sure I understand the question here, this is for testing the cases with swapped arguments.

> Username: barendgehrels  
> Created_at: 2021-11-03 18:55:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r742243201  

I referred to the `TODO`. Probably it's already done.

> Username: vissarion  
> Created_at: 2021-11-04 10:51:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r742725145  

Yes, I removed the comment, thanks.

---

📁 test/algorithms/closest_points/common.hpp

```diff
 139 |+                     Strategy const& strategy,
 140 |+                     bool const& swap_geometries,
 141 |+                     bool const& default_strategy)
```

> Username: barendgehrels  
> Created_at: 2021-11-03 09:54:01 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741765185  

Minor, we normally just pass `bool`. It's done like that below too.


📁 include/boost/geometry/algorithms/detail/closest_points/point_to_geometry.hpp

```diff
 128 |+         
 129 |+         auto closest_point = strategies.closest_points(point, range)
 130 |+             .apply(point, *it_pair.first, *it_pair.second);
```

> Username: barendgehrels  
> Created_at: 2021-11-03 11:19:55 UTC  
> Updated_at: 2021-11-03 11:23:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r741841310  

Minor, we usually use `it_pair->first` and `it_pair->second`

> Username: vissarion  
> Created_at: 2021-11-05 08:22:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r743462109  

Note that here `it_pair` is a pair of iterators not an iterator of a pair.

> Username: awulkiew  
> Created_at: 2021-11-05 10:17:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r743538035  

If the name can be confusing maybe we could change it? This code is taken from `distance()` but here the type is not explicitly stated the only identifier that describes this variable in the code is its name. For me this either looks like an iterator to pair or a range represented as pair of iterators but is neither of these things. It's a representation of a segment. We already have names like `referring_segment` and `pointing_segment` in the code. My point is that the name should probably contain the word `segment`.

> Username: vissarion  
> Created_at: 2021-11-05 13:53:50 UTC  
> Updated_at: 2021-11-05 13:53:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r743680938  

ok I agree, what about `closest_segment` ? It is the closest segment in `range` to the query `point`.

> Username: awulkiew  
> Created_at: 2021-11-05 16:19:34 UTC  
> Updated_at: 2021-11-05 16:19:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#discussion_r743802444  

`closest_segment` looks good.


---

## Comment 35

> Username: barendgehrels  
> Created_at: 2021-11-03 12:44:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#issuecomment-958997023  

> Thanks for the detailed review. I addressed all the comments. The main change is that closest_points will not support legacy strategies. I think this is OK for new algorithms @barendgehrels what do you think?  
  
I think it's fine, for new algorithms, to not support legacy strategies. We're moving on.

---

## Comment 36

> Username: vissarion  
> Created_at: 2021-11-03 15:03:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#issuecomment-959362700  

> Cool that we have this now.   
>I think some of the docs are not in this PR, could you add them?   
  
Ι prefer to complete the documentation in the last PR of `closest_points` sequel.   
  
>I added a few comments, can you address them if you agree?   
  
Thanks! I answered to all of them.  
  
> What is the relation from `closest_points` to `closest_feature` - do we need to keep both?  
  
`closest_feature` is more like a set of utilities that compute the distance and the closest feature between ranges and geometries. They are used by `distance` and `closest_points` algorithms.

---

## Comment 37

> Username: barendgehrels  
> Created_at: 2021-11-03 18:54:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/923#issuecomment-959827808  

> Ι prefer to complete the documentation in the last PR of `closest_points` sequel.  
  
Sure, that's fine, I'll approve, and thanks for processing and answering.

---
