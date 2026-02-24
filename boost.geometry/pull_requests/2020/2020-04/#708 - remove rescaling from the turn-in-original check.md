# #708 [buffer] remove rescaling from the turn-in-original check [Merged]

> Username: barendgehrels  
> Created at: 2020-04-29 15:12:01 UTC  
> Updated at: 2020-09-09 09:07:57 UTC  
> Merged at: 2020-05-06 08:24:54 UTC  
> Closed at: 2020-05-06 08:24:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/708  

This is the follow up, it is shorter

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-29 17:07:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/708#pullrequestreview-402867995  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_original_visitor.hpp

```diff
  39 |+             <
  40 |+                 box_tag, typename cs_tag<Box>::type
  41 |+             >::type expand_strategy_type;
```

> Username: awulkiew  
> Created_at: 2020-04-29 17:07:12 UTC  
> Updated_at: 2020-04-29 17:07:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r417474099  

The CS should depend on the strategy passed by the user instead of being derived from the Box which AFAIU is based on the Point type of the geometry.

> Username: barendgehrels  
> Created_at: 2020-04-29 17:51:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r417501481  

The strategy passed (currently `IntersectionStrategy`) defines a segment strategy, and not a box or point strategy. So it is better to define it locally.  
  
Besides that, I thought more about this and I don't see any point for a user-defined expand strategy... They are not earth-model dependant, the only special thing is that the dateline and the poles should be handled correctly. Do I miss something?

> Username: awulkiew  
> Created_at: 2020-04-29 18:29:15 UTC  
> Updated_at: 2020-04-29 18:36:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r417524239  

> The strategy passed (currently IntersectionStrategy) defines a segment strategy, and not a box or point strategy.  
  
It also defines the desired CS of the operation passed by the user. If you pass a strategy explicitly it defines how the operations are done. Geometries are used only at the highest level of an algorithm to get the default main strategy in order to pass it further. Internally the CS is defined always by the strategies.  
  
> So it is better to define it locally.   
  
It's easier for sure. But it's wrong according to the design rationale of the library.  
  
> I don't see any point for a user-defined expand strategy  
  
Box, expanding, envelope, etc. are different in cartesian vs spherical/geographic. Just like any other CS-specific operation. Call e.g. `expand(BOX(170 0 179 1), POINT(-170 0))` and you'll see the difference which would be `BOX(-170 0 179 1)` in cartesian vs `BOX(170 0 190 1) in spherical/geographic`. The case of a box expanded with another box is similar. And I didn't even mention boxes touching poles where things get even wierder.  
  
With that said this will work, the boxes will simply be bigger than they could be. However we considered passing cartesian geometries and geographic strategy. What if you do the opposite? You have geographic geometries and you pass cartesian strategy. Now this expand will take the winding of the CS at the antimeridian and the poles into account when the user wanted to calculate the cartesian intersection/buffer. Example:  
1. User stores geographic geometries using `cs::geographic` containing e.g. `POINT(0, 50)`  
2. User performs a map projection and stores the result using the same point type, now this point is e.g. `POINT(433000 526440)`  
3. User calculates set operation or buffer passing the cartesian strategy explicitly (assuming that it is possible to pass a strategy into `buffer`).  
  
At 3, internally at the line we're talking about here the CS is taken from the geometry and the geographic expand is calculated e.g. for this point `POINT(433000 526440)`. Technically this point is invalid geographical point. In practice `expand` will try to normalize the coordinates somehow. The result of this `expand` is going to be some arbitrary junk in the range of valid ellipsoidal coordinates not representing the cartesian point at all.

> Username: barendgehrels  
> Created_at: 2020-05-06 08:24:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r420622122  

Thanks for your answer. But this was not exactly my point. I know that expand should be different for cartesian and for spherical/geographic. But the CS is already part of the geometry, as you know. And that is exactly how I am deriving it.  
  
I don't get the point about passing a cartesian geometry and a geographic strategy. Shouldn't that be avoided? But the way I implement it here there is no risk for that.  
  
So my question is again: what is the need for a user-specific expand strategy? Will there ever be an Earth model relevant there?

> Username: awulkiew  
> Created_at: 2020-05-06 14:00:42 UTC  
> Updated_at: 2020-05-06 14:00:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r420814857  

> I don't get the point about passing a cartesian geometry and a geographic strategy.  
  
I always thought this was the intention. For example it was always possible to call:  
```  
distance(cart_pt1, cart_pt2, haversine<>());  
```  
and the result was correct and defined by the strategy.  
  
It was also always possible to call `get_as_radian` with cartesian points.  
  
> Shouldn't that be avoided?  
  
The code compiles, the library doesn't complain about it, there is no static assert, there is no explicit information in the documentation about it AFAICS. You say "avoided" as if it was "it sometimes works and sometimes doesn't", it isn't a particularly firm and well-defined position. :)  
  
I'm surprised that this issue is raised only now. After these pull requests adding the propagation of strategies and `cs::undefined` were merged:  
https://github.com/boostorg/geometry/pull/533  
https://github.com/boostorg/geometry/pull/605  
So yes, it is possible to create geometries with undefined CS and then the user has to pass the strategy and the information about the CS is propagated downwards to all algorithms. As it is possible to not pass a strategy and use the default one taken from the geometry. So it works both ways where the strategy has the priority.  
  
> But the way I implement it here there is no risk for that.  
  
You mean this code will only be called in one CS?  
  
> So my question is again: what is the need for a user-specific expand strategy? Will there ever be an Earth model relevant there?  
  
It doesn't matter if we consider the earth model or the strategy containing it. The earth model also has the information about the CS. My reasoning is based on the fact that if the user passes a strategy, this strategy defines a CS/model and that all of the strategies used in an algorithm has to be consistent with each other.  
  
At this point we have a few options:  
0. continue without expressing explicitly what the behavior is when CSes of geometries and strategies are mixed  
1. enforce a match of the CS between geometries and strategies with a static assert which I think is a breaking change at this point  
2. only use the CS information of the geometry, impossible since strategies can be passed  
3. only use the CS information of the strategy, impossible as well because of the default behavior is defined this way  
4. allow both with one preferred behavior, define all CS-related information in the strategy with the geometry defining the default behavior.  
  
I thought that 4. was always the intention but we were not pursuing it well enough and were conveniently avoiding it.  
  
Btw, if the propagation of the model is the only problem here, why do we even need an umbrella strategy? If the CS is defined by the geometry and the only purpose of the strategy passed by the user is to define the model then we could simply take this model and create all default strategies below from it. But the model also defines the CS. We'd still have the same problem here, i.e. if the model was the ellipsoid should we use cartesian expand or geographic/spherical expand?  
  
My reasoning is that either we define everything CS-related in a strategy or neither of it. Otherwise it's confusing which information is taken from where.

> Username: barendgehrels  
> Created_at: 2020-05-06 16:26:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r420924677  

Thanks, I'll come back to this later.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2020-04-30 16:34:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#issuecomment-621964879  

I raised an issue regarding the strategies. However AFAIU this is how the `buffer` worked before this PR was proposed and the purpose of this PR is not to add propagation of strategies but to remove rescaling. This is why I'm ok with merging it as it is.

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-30 16:35:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/708#pullrequestreview-403690110  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_original_visitor.hpp

```diff
 233 | 
 229 |-         if (geometry::disjoint(turn.robust_point, original.m_box))
 234 |+         if (geometry::disjoint(turn.point, original.m_box))
```

> Username: awulkiew  
> Created_at: 2020-04-30 16:35:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r418141289  

Just for completness, here the strategy is needed as well. I'm ok with merging it though.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-09-02 15:18:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/708#pullrequestreview-480932521  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 278 |     // but also for large joins with many points
 296 |-     typedef geometry::sections<normal_box_type, 2> sections_type;
 279 |+     typedef geometry::sections<box_type, 2> sections_type;
```

> Username: awulkiew  
> Created_at: 2020-09-02 15:18:42 UTC  
> Updated_at: 2020-09-02 15:19:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r482155373  

@barendgehrels I stumbled upon a problem regarding this change. The sections and their boxes are used in partition:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp#L592  
and then deeper here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp#L224-L225  
and then here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/buffer/get_piece_turns.hpp#L161-L164  
then here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/sections/section_functions.hpp#L136  
which as you can see from the parameters relies on the fact that both points and boxes are robust.  
And finally here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/sections/section_functions.hpp#L136  
which causes robust point be compared with non-robust boxes.  
  
Now, I didn't check how sections/boxes are created. If boxes are created from robust points and then simply the value is stored using `double` then I guess we can loose precision when integer values are compared with doubles in which case the bounding box may not cover the robust point (maybe?). If they are created from original points then comparing them with robust points doesn't make any sense because robust coordinates are rescaled.  
  
From the values I see:  
```  
point            | {6.3920000000000003, 2.5440000000000000}    | boost::geometry::model::point<double,2,boost::geometry::cs::cartesian>  
robust_point     | {152743, -763011}                           | boost::geometry::model::point<__int64,2,boost::geometry::cs::cartesian>  
other_robust_box | {{-428079.00000000012, -554791.00000000012},  
                    {428086.00000000012, -554790.99999999988}} | boost::geometry::model::box<boost::geometry::model::point<double,2,boost::geometry::cs::cartesian> >  
point_robust_box | {{-360956.00000000006, -763011.00000000012},  
                    {152743.00000000003, -78078.999999999985}} | boost::geometry::model::box<boost::geometry::model::point<double,2,boost::geometry::cs::cartesian> >  
```  
I think it is the former, so normal point is passed here, sections use the same coordinate type as the normal point (`double`) but they hold the rescaled values. If that's the case then changing `sections_type` to use `box_type` instead of `robust_box_type` was wrong in the worst case and useless in the best case because values are still rescaled anyway.  
  
This is non-detectable for native integer robust types but if you wanted to use MultiPrecision integral type as robust type then the less-comparison between MultiPrecision integral and `double` does not exist. I have a branch where this is done if you want to play with it.  
  
What do you think we should do here?  
  
All of this can be reproduced e.g. with `buffer_multi_polygon.cpp` test case.  
  
P.S. I also don't understand why does buffer deal with sections manually like that. Partition should do the space partitioning and call visitor accordingly. An algorithm should not interfere with that. But that's a different story.

> Username: barendgehrels  
> Created_at: 2020-09-03 11:44:43 UTC  
> Updated_at: 2020-09-03 11:44:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r482915003  

Hi Adam, thanks, I think I recently saw this too and fixed it in my next PR, which is not yet ready.  
I’ll come back to it next Wednesday.

> Username: barendgehrels  
> Created_at: 2020-09-09 09:07:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/708#discussion_r485458659  

I can reproduce it, but it's a different thing than I thought. I'm working on it.


---
