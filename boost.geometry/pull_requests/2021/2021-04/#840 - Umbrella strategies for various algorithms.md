# #840 Umbrella strategies for various algorithms. [Merged]

> Username: awulkiew  
> Created at: 2021-04-01 18:41:18 UTC  
> Updated at: 2021-05-21 14:55:46 UTC  
> Merged at: 2021-05-21 14:55:46 UTC  
> Closed at: 2021-05-21 14:55:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/840  

Support is added in the following algorithms:  
- buffer (now it has custom strategy because simplify is used internally so it cannot use relate strategy)  
- centroid  
- densify  
- discrete_frechet_distance  
- discrete_hausdorff_distance  
- length  
- line_interpolate  
- perimeter  
- simplify  
  
Centroid legacy strategies now does not require passing point types as template parameters. These parameters are now ignored.  
  
Simplify agnostic strategy is now algorithm's detail.  
  
Added dummy geometries which allow to create strategies in cases when geometry variables are not accesible or the access is not straightforward (e.g. would require to call operator/function or rearrange the algorithm).  
  
Strategies are passed correctly in some places where default one was used improperly.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2021-04-08 07:16:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#issuecomment-815515043  

This PR is very welcome. I will review it soon.  
  
You asked me what compiler I'm using: `clang version 5.0.2 (tags/RELEASE_502/final)`

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-04-28 07:46:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/840#pullrequestreview-646670399  

I'm OK, I didn't review all lines, but the move to umbrella strategies is welcome.

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 117 |  
 117 |-         ps_strategy_type const strategy = strategies.distance(box_points.front(), segment);
 118 |+         ps_strategy_type const strategy = strategies.distance(dummy_point(), dummy_segment());
```

> Username: barendgehrels  
> Created_at: 2021-04-28 07:14:54 UTC  
> Updated_at: 2021-04-28 07:46:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r621879479  

So we need an instance of a specific type of strategy and therefore we pass the dummies.  
Can't we say, for example: `stragies.template distance<point_tag, segment_tag>()` instead? Or are there disadvantages?  
What if the returned strategy depends on coordinate system?

> Username: awulkiew  
> Created_at: 2021-04-28 13:11:16 UTC  
> Updated_at: 2021-04-28 13:11:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r622163863  

> Can't we say, for example: `stragies.template distance<point_tag, segment_tag>()` instead?  
  
Yes, we could. At least I think it should be possible to implement this directly inside the umbrella strategies like the current version is. It'd require to modify all of the umbrella strategies.  
  
> Or are there disadvantages?  
  
It's longer and requires the additional `template` keyword. In most of the cases it's enough to simply pass geometry that you already have. So:  
```  
strategies.distance(point, segment);  
strategies.distance(dummy_point(), dummy_segment());  
```  
vs  
```  
strategies.template distance<typename tag<point>::type, typename tag<segment>::type>();  
strategies.template distance<point_tag, segment_tag>();  
```  
  
> What if the returned strategy depends on coordinate system?  
  
The umbrella strategy defines the coordinate system. It's created on the very top of the algorithm. By default it's created based on the coordinate type of the geometries (like it was with legacy strategies). All of the legacy strategies returned by it should use the same coordinate system in order to be consistent with each other. Umbrella strategy is passed internally into all algorithm that may be called by upper-level algorithms. The coordinate systems of geometries are irrelevant. It's because otherwise one could pass upper-level strategy of one CS into an upper-level algorithm which then would call lowel-level algorithm where inconsistent CS would be taken from geometry.

> Username: barendgehrels  
> Created_at: 2021-04-28 20:00:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r622496107  

Right, thanks for your answers! I'm OK with passing the dummies to avoid longer usage. Usually the geometry will be available, and otherwise, the dummy-definition is quite concise, I noticed. So it's fine from my perspective.


📁 include/boost/geometry/strategies/buffer/services.hpp

```diff
   1 |+ // Boost.Geometry
   2 |+ 
   3 |+ // Copyright (c) 2021, Oracle and/or its affiliates.
```

> Username: barendgehrels  
> Created_at: 2021-04-28 07:42:18 UTC  
> Updated_at: 2021-04-28 07:46:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r621898900  

Apparently the copyright message (with my name) is gone, can you keep the copyright of the moved messages?  
I would like to keep seeing my name in the files I wrote :-)  
  
Or actually, these files are probably brand new files, new syntactic sugar. Then it's fine, you can ignore this comment.

> Username: awulkiew  
> Created_at: 2021-04-28 11:57:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r622104167  

Yes, they are new. You can always add something in this file together with copyright message. :)


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2021-05-17 14:55:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/840#pullrequestreview-660997262  

📁 include/boost/geometry/algorithms/detail/dummy_geometries.hpp

```diff
  43 |+ template <> struct tag<geometry::detail::dummy_segment> { typedef segment_tag type; };
  44 |+ template <> struct tag<geometry::detail::dummy_box> { typedef box_tag type; };
  45 |+ template <> struct tag<geometry::detail::dummy_linestring> { typedef linestring_tag type; };
```

> Username: vissarion  
> Created_at: 2021-05-17 13:55:21 UTC  
> Updated_at: 2021-05-17 14:55:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r633552844  

dummy geometries other than point, segment and box seem unused. Do we need to define them now or leave it and define them on demand? Are they expected to be used ?

> Username: awulkiew  
> Created_at: 2021-05-19 09:55:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r635089359  

Yes, right now they are not used but technically they could. I defined them for the sake of completeness.

> Username: vissarion  
> Created_at: 2021-05-19 10:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r635117869  

Yes, I was thinking whether it was clearer to define them on demand but I am OK with leaving it as is.


📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 710 |+         using strategies::simplify::services::strategy_converter;
 711 | 
 481 |-         BOOST_CONCEPT_ASSERT(
```

> Username: vissarion  
> Created_at: 2021-05-17 13:58:18 UTC  
> Updated_at: 2021-05-17 14:55:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r633555467  

Educative question: is this not used anymore?

> Username: awulkiew  
> Created_at: 2021-05-19 09:54:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r635088166  

I guess it'd still make sense to check the legacy strategy after it's retrieved from umbrella strategy. I'll look into it.

> Username: awulkiew  
> Created_at: 2021-05-19 10:17:37 UTC  
> Updated_at: 2021-05-19 10:17:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/840#discussion_r635104460  

What I wrote before is wrong. There is no simplify strategy anymore. It was an agnostic strategy moved to algorithms.


---
