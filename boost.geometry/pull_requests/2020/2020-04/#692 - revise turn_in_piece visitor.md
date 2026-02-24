# #692 [buffer] revise turn_in_piece visitor [Merged]

> Username: barendgehrels  
> Created at: 2020-04-01 11:54:35 UTC  
> Updated at: 2020-05-06 08:42:16 UTC  
> Merged at: 2020-04-29 11:48:20 UTC  
> Closed at: 2020-04-29 11:48:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/692  

- avoid side_of_intersection (necessary for rescale removal)  
- use new abstraction piece_border to verify point-in-piece (necessary for rescale removal  
- this replaces the robust_ring  
- and much more  
  
Please start the review. I want to add unit tests next week, for the new class `piece_border` and the new strategy `turn_in_ring_winding`. The last one is more specific than other strategies and maybe I should move it to the buffer folder. Also it is a bit (but by far not as much as replaced implementation) dependent on Cartesian.  
  
It is larger than I initially thought but it is quite a cleanup.  
(Edited): performance is just slightly better, measured on `recursive_polygons_buffer.cpp`:  
560 s (new) vs 600 s (old) and it makes slightly less errors (there are still errors, to be looked at later when rescaling is gone, this test is quite merciless)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-04-01 11:55:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-385512595  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 354 | 
 355 |+     template <typename Turn>
 356 |+     std::string sidx(Turn const& turn)
```

> Username: barendgehrels  
> Created_at: 2020-04-01 11:55:28 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r401558608  

~~Oops, I still have to remove this, will do~~  
:heavy_check_mark:


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2020-04-01 11:56:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-385513473  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
   7 |- // Modifications copyright (c) 2016-2018 Oracle and/or its affiliates.
   8 |- // Contributed and/or modified by Adam Wulkiewicz, on behalf of Oracle
   3 |+ // Copyright (c) 2020 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: barendgehrels  
> Created_at: 2020-04-01 11:56:51 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r401559298  

This file is for 99% replaced (I started with a new file).  
So I removed the Oracle copyright, but if you want to keep it, OK for me.

> Username: awulkiew  
> Created_at: 2020-04-22 22:09:27 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413366802  

I have no opinion. I'm not a legal expert. :)  
I'd rather worry about files to which you copied the code from this one because I see a lot of changes done by me regarding the propagation of strategies and one change done by Menelaos in the past so technically if you copied any part affected by us into a different file this file should have all of the copyrights too I think. But this is only my guess.

> Username: awulkiew  
> Created_at: 2020-04-22 22:18:01 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413370805  

When I view this version of the file and click "Blame" I still see some changes made by me and Menelaos, see:  
https://github.com/boostorg/geometry/blame/600e4a32a1d57638f6050b238fadb5e1a3dde2eb/include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp  
So even though the majority of the code was removed our changes are still there. This one even adds my copyrights: https://github.com/boostorg/geometry/commit/fd72a1aa16a71ac465db334890964a3799bf5686  
Not sure why, though, maybe I changed something during weekend ;)

> Username: barendgehrels  
> Created_at: 2020-04-29 08:04:26 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417135916  

> When I view this version of the file and click "Blame" I still see some changes made by me and Menelaos, see:  
  
No problem, I'll put it back.


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2020-04-08 11:32:38 UTC  
> Updated_at: 2020-04-15 12:07:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-610905635  

There are 4 unit test cases, looking like this the image below.  
- the green polygon is a "piece" (here defined by a piece_border)  
- red points should be classified as inside (T of true)  
- green points as outside (F of false)  
- blue points are on the offsetted ring, should be outside  
- orange points are on the original ring, should be inside (but NOT for a onesided line buffer)  
- cyan points are on the piece border, but not on original, neither on offsetted ring, and should be inside (unless it coincides with a line buffer end point with flat ends)  
  
![piece_border](https://user-images.githubusercontent.com/334849/78784130-14938b80-79a5-11ea-95b7-276f3803e298.png)

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2020-04-22 08:21:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-617628579  

@awulkiew  and @vissarion  are you OK to merge this?

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2020-04-22 09:08:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-397992586  

It is ok for me. Sorry for the late review I though it was a PR in progress.

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2020-04-22 09:14:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-397997976  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 238 |     struct original_ring
 239 |     {
 240 |+         typedef geometry::model::box<robust_point_type> robust_box_type;
```

> Username: barendgehrels  
> Created_at: 2020-04-22 09:14:57 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r412813487  

In the next (smaller) step I will update point-in-original-ring with non rescaled types


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2020-04-22 09:49:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-617674830  

>   
>   
> It is ok for me. Sorry for the late review I though it was a PR in progress.  
  
It was in progress earlier indeed. Thanks for reviewing!

---

## Comment 8

> Username: awulkiew  
> Created_at: 2020-04-22 14:55:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-617830151  

@barendgehrels  Sorry for a late response. I'll review it today.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 16:57:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398390937  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_policies.hpp

```diff
 205 |+         if (piece.m_piece_border.m_has_envelope)
 206 |+         {
 207 |+             geometry::expand(total, piece.m_piece_border.m_envelope);
```

> Username: awulkiew  
> Created_at: 2020-04-22 16:57:47 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413153697  

ExpandBoxBox strategy is needed here.

> Username: barendgehrels  
> Created_at: 2020-04-22 17:34:26 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413180691  

Thanks, will fix (this probably was already the case earlier, but will fix anyway)  
Fixed here :heavy_check_mark:


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 16:58:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398391481  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_policies.hpp

```diff
 240 |+     static inline void apply(Box& total, Turn const& turn)
 241 |+     {
 242 |+         geometry::expand(total, turn.point);
```

> Username: awulkiew  
> Created_at: 2020-04-22 16:58:27 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413154185  

ExpandBoxPoint strategy is needed here.

> Username: barendgehrels  
> Created_at: 2020-04-29 08:34:53 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417152303  

Not supported... ~~There is something needed in the handling of expand with a strategy and a point. I leave this to you to avoid expanding the scope of this PR~~  
EDIT: the expand strategy by intersection is defined as `typedef expand::cartesian_segment expand_strategy_type;` and does not support the call to expand with a `point` as a second argument.   
So this requires another expand strategy then? Another reason to move to umbrella strategy.

> Username: barendgehrels  
> Created_at: 2020-04-29 09:58:12 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417199233  

The expand strategies do not have the Earth model anyway (is there ever a reason to do that?).  
So I can define and use them locally here, they don't need to be passed from the IntersectionStrategy.  
Done :heavy_check_mark:

> Username: awulkiew  
> Created_at: 2020-04-29 16:02:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417430981  

> The expand strategies do not have the Earth model anyway  
  
That's because they are the same as the spherical ones and the unit sphere is enough as a model. Still it's different than cartesian.

> Username: barendgehrels  
> Created_at: 2020-04-29 16:13:17 UTC  
> Updated_at: 2020-04-29 16:13:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417438601  

>   
>   
> > The expand strategies do not have the Earth model anyway  
>   
> That's because they are the same as the spherical ones and the unit sphere is enough as a model. Still it's different than cartesian.  
  
Yep - so I think what I now submitted (and merged) is fine?

> Username: awulkiew  
> Created_at: 2020-04-29 17:57:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417505369  

Almost ;) The CS should be taken from the strategy passed by the user instead of a geometry.

> Username: barendgehrels  
> Created_at: 2020-04-29 17:59:38 UTC  
> Updated_at: 2020-04-29 17:59:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417507014  

See my message in the other PR - the passed strategy doesn't compile for points, because it's defined as a segment-specific strategy (for boxes it does compile but still wrong because a box is not a segment)

> Username: awulkiew  
> Created_at: 2020-04-29 19:49:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417569780  

I'm ok with leaving it as it is. I'll go back into it later if you don't want to deal with it. But I'll describe what we're dealing with here for informative purposes.  
  
I'll write how this should work in general. At the top-most level the user passes a strategy (doesn't matter which one). This strategy at the top-most level can also be created based on the geometry if the user doesn't pass it explicitly. Then the strategy is passed below into the internals of an algorithm. This strategy defines the CS of all of the operations below (or defines all of the operations themselves as sub-strategies, it doesn't matter). What matters is that the information about the CS is passed by the user with the top-most level strategy.  
  
So here `expand` is called. If the user's intention (by proxy) is to perform expansion of a box with some other geometry in a specific CS the following strategies should be used:  
* cartesian  
  * point - `strategy::expand::cartesian_point`  
  * box - `strategy::expand::cartesian_box`  
  * segment - `strategy::expand::cartesian_segment`  
* spherical  
  * point - `strategy::expand::spherical_point`  
  * box - `strategy::expand::spherical_box`  
  * segment - `strategy::expand::spherical_segment`  
* geographic  
  * point - `strategy::expand::spherical_point`  
  * box - `strategy::expand::spherical_box`  
  * segment - `strategy::expand::geographic_segment`  
  
In this case we're talking about `expand(box, point)` so the strategy is either `strategy::expand::cartesian_point` or `strategy::expand::spherical_point`. And at the top-most level of `buffer` the strategy created is the `IntersectionStrategy`. So one of the two `expand` strategies has to be derived from `IntersectionStrategy`. At least this is how it is right now, if we change it in the future it might be different (e.g. it might be derived from an `UmbrellaStrategy`). But right now, the `IntersectionStrategy` directly only defines Box (`expand_box_strategy_type`) and Segment (`expand_strategy_type`) expand strategies. So you have several options here:  
1. Take it from one of the underlying strategies, e.g.:  
```  
IntersectionStrategy::template point_in_geometry_strategy<BoxPoint, TurnPoint>::expand_point_strategy_type  
```  
2. Create default one based on `IntersectionStrategy::cs_tag`:  
```  
strategy::expand::services::default_strategy<point_tag, IntersectionStrategy::cs_tag>::type  
```  
This will probably not compile right now for spherical because for spherical `IntersectionStrategy` the `cs_tag` is `spherical_tag` and `default_strategy` is not specialized for this tag so with this you'd have to add specialization here:  
https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/spherical/expand_point.hpp#L205-L221  
3. Create it based on `IntersectionStrategy::cs_tag` without using `default_strategy` with, e.g.:   
```  
typedef typename boost::if_c  
    <  
        boost::is_same<typename IntersectionStrategy::cs_tag, cartesian_tag>::value,  
        strategy::expand::cartesian_point,  
        strategy::expand::spherical_point,  
    >::type expand_point_strategy_type;  
  
// you could add some check for safety or not:  
  
static const bool cs_check =  
    boost::is_same<typename IntersectionStrategy::cs_tag, cartesian_tag>::value  
    || boost::is_same<typename IntersectionStrategy::cs_tag, spherical_tag>::value  
    || boost::is_same<typename IntersectionStrategy::cs_tag, geographic_tag>::value;  
BOOST_MPL_ASSERT(cs_check);  
```

> Username: barendgehrels  
> Created_at: 2020-05-06 08:39:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r420630782  

Thank you.  
  
I think you're missing one important point I make. **It does not compile.** And not because the CS is wrong, it does not compile because the expand strategy, defined in intersection strategy, is defined for `segment` and not on `box` or `point`.  
Therefore **it does not compile if you expand with a point.**  
  
And this is an important flaw of how the strategy is passed. We might solve it by adding two other strategies to intersection_strategy. But I'm really opposed to that idea.  
  
I still don't know why expand strategy should be passed at all. For other strategies, I understand the rationale (distance, area, point-in-polygon, side, etc). But this one is just an implementation detail, different for different cs, true, but not user-dependent or earth-model dependent.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:04:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398396197  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 138 |-             robust_point_type
 139 |-         >::type robust_comparable_radius_type;
 135 |+     typedef geometry::model::box<point_type> normal_box_type;
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:04:16 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413158438  

Maybe simply `box_type`? The robust ones will go anyway.

> Username: barendgehrels  
> Created_at: 2020-04-22 17:35:14 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413181222  

I renamed it in the next PR (which is nearly ready) because indeed the robust one is gone then.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398404097  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 359 |+         for (It it = begin; it != end; ++it)
 360 |+         {
 361 |+             geometry::expand(envelope, *it);
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:00 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413165637  

This needs a strategy.

> Username: barendgehrels  
> Created_at: 2020-04-29 10:25:45 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417213401  

Done :heavy_check_mark:


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398404368  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 221 |+         }
 222 |+         expand_envelope(envelope, m_ring->begin() + m_begin, m_ring->begin() + m_end);
 223 |+         expand_envelope(envelope, m_originals.begin(), m_originals.begin() + m_original_size);
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:21 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413165871  

They need a strategy.

> Username: barendgehrels  
> Created_at: 2020-04-29 10:26:02 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417213562  

It's a local function, but the final expand gets a strategy now


---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398404677  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 172 |+     void get_properties_of_border(bool is_point_buffer, Point const& center)
 173 |+     {
 174 |+         m_has_envelope = calculate_envelope(m_envelope);
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:14:44 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413166109  

This needs a strategy.

> Username: barendgehrels  
> Created_at: 2020-04-29 10:26:12 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417213653  

Same here


---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:17:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398406848  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 176 |+         {
 177 |+             // Take roundings into account, enlarge box
 178 |+             detail::buffer::buffer_box(m_envelope, 1.0e-3, m_envelope);
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:17:26 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413168122  

What is the source of the `1.0e-3` number? Shouldn't it depend on the machine epsilon and the coordinates themselves? For very small coordinates this number if too big, it's not a problem though. However for very big coordinates this number will be too small.

> Username: barendgehrels  
> Created_at: 2020-04-22 17:36:12 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413181909  

It's actually the same as we did in the sectionalize.  
~~Good point - I will take over what we did there.~~  
Using expand_by_epsilon :heavy_check_mark:


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:19:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398408612  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 201 |+         BOOST_GEOMETRY_ASSERT(end <= boost::size(ring));
 202 |+ 
 203 |+         m_ring = &ring;
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:19:37 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413169668  

Can `Ring` be a user-defined type or is it our type and we can be sure that the `operator&` is not defined? Because otherwise `boost::addressof` should be used here.

> Username: barendgehrels  
> Created_at: 2020-04-22 17:36:52 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413182439  

No, the ring is an offsetted ring and defined by ourselves.  
Or, actually, I can of course use `boost::addressof` anyway here.  
:heavy_check_mark:


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 17:21:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398410299  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 342 |+         typedef typename geometry::coordinate_type<Point>::type coordinate_type;
 343 |+         typedef geometry::detail::distance_measure<coordinate_type> dm_type;
 344 |+         dm_type const dm = geometry::detail::get_distance_measure<cartesian_tag>(point, p1, p2);
```

> Username: awulkiew  
> Created_at: 2020-04-22 17:21:44 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413171099  

Shouldn't this tag be taken from the Strategy?

> Username: barendgehrels  
> Created_at: 2020-04-22 17:37:12 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413182646  

ah, yes, of course. ~~Will fix.~~  
:heavy_check_mark:


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 21:44:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398612163  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 145 |+     //! Returns true if there is a ring, the ring has one or more points,
 146 |+     //! and the original has one or two points
 147 |+     bool valid() const
```

> Username: awulkiew  
> Created_at: 2020-04-22 21:44:06 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413354269  

Could this function have a different name? Validity in our library has a different meaning. E.g. something directly referring to what this function return, like `is_non_empty_ring_set()` or something. What do you think?

> Username: barendgehrels  
> Created_at: 2020-04-29 08:55:40 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417164449  

-> ring_or_original_empty() (with reversed meaning and now private)  
:heavy_check_mark:


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 21:50:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398615457  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 242 |+         // part (one segment), and only then the offsetted part
 243 |+         // (probably more segments, check monotonicity)
 244 |+         geometry::strategy::buffer::turn_in_ring_winding<coordinate_type> tir;
```

> Username: awulkiew  
> Created_at: 2020-04-22 21:50:02 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413357285  

Right, so this is a cartesian strategy right? I assume it is and in the future it should be derived from the strategy passed by the user because in general should depend on the CS. So in geographic CS the ellipsoid should be propagated here. Could we for now implement it in such a way that when someone tries to pass a non-cartesian geometry it does not compile? E.g. add static assert checking cs_tag == cartesian_tag or something. This way it will be easier to find it in the future. It might be done here or in the strategy itself.

> Username: awulkiew  
> Created_at: 2020-04-22 21:52:58 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413358747  

Actually, the strategy passed by the user should be checked to be cartesian strategy, not the geometry. The intersection strategy defines cs_tag AFAIR so this should be easy.

> Username: barendgehrels  
> Created_at: 2020-04-29 08:57:43 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417165667  

I leave this for now, first it was already targetted to cartesian (I made it less dependent by removing side_by_intersection and other specific things, but this PR does not have the purpose to make it completely cs agnostic). Second, buffer of geographic points is already supported so I should not blindly check the cs_tag.

> Username: awulkiew  
> Created_at: 2020-04-29 16:14:18 UTC  
> Updated_at: 2020-04-29 16:14:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417439280  

> this PR does not have the purpose to make it completely cs agnostic  
  
Right.  
  
> Second, buffer of geographic points is already supported so I should not blindly check the cs_tag.  
  
Ok, fixing it now would probably be outside of this PR.  
  
Also my thoughts regarding this issue. Internally `turn_in_ring_winding` relies on cartesian `model::infinite_line` (unless I'm missing something). It is therefore a cartesian strategy. Using it in spherical and geographic buffer is technically wrong (unless you're certain that the distances passed by the user are small which we can't guarantee). Checking it wouldn't be blind, it'd make sure that the user doesn't get the incorrect results thinking they are correct. I'd rather argue that using it in the first place for all CSes was blindly assuming things. If it is used for geographic points then it means it's a bug. It should probably have been modified along with introducing support for geographic points. My guess is that it was not because the compiler didin't argue about it, if the CS was checked here the compiler would point to the place that is wrong in the code.

> Username: barendgehrels  
> Created_at: 2020-04-29 17:57:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417505500  

Any buffer around a single point will not call this code.  
Only when multi-points (or linear/poly geometries) are buffered, then this code would be called.  
But if we make a compile-time check here, the geo-point-buffer won't compile. That is the reason I cannot do it now. It was already like this before (but then not `infinite_line` (indeed) but `side_by_intersection` and other things which were cartesian related)

> Username: awulkiew  
> Created_at: 2020-04-29 18:48:34 UTC  
> Updated_at: 2020-04-29 18:48:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417535520  

Ok, got it, no problem. As I said, fixing it is outside of the scope of this PR.


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 22:01:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398621522  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 485 |+         {
 486 |+             segment_type const s(*previous, *it);
 487 |+             radius_type const d = geometry::comparable_distance(center, s);
```

> Username: awulkiew  
> Created_at: 2020-04-22 22:01:36 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413363132  

This needs a strategy. But there is a problem with it because it cannot be derived from the intersection strategy since the intersection strategy is lower-level than the distance point/segment strategy I think, so there would be a cyclic-dependency. I know that because recently I looked into this while checking whether or not it is possible to use `buffer` with `cs_undefined` CS and it is not possible right now because internally, at the top-most level the default intersection strategy is created and internally the default `comparable_distance` is called. But anyway, the solution I came up with for this cyclic-dependency is to implement a "proper" CS-specific buffer strategy providing both CS-specific intersection and comparable_distance strategies. So this would be a greater change outside of the scope of this PR I think. Therefore I propose to leave this at is is for now and later I'll propose my solution for this issue (I'd like to propose some more changes too along with it). Do you agree?

> Username: barendgehrels  
> Created_at: 2020-04-29 09:05:22 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417169957  

We discussed a while ago: I'm quite against adding sub-strategies to the `IntersectionStrategy`. There should be a proper `UbrellaStrategy` and this is *not* the IntersectionStrategy. Because why? It doesn't use most of the strategies defined there (I think it uses only `side` and nothing more). It's just the wrong place. Why should the intersection of two segments define a `point_in_polygon`? I just don't get that.  
  
Well, I understand how it happened - there needed to be a place and this strategy was passed everywhere - so it was added there. But it is inconvenient and should be refactored.  
  
So yes, if this could be fixed by moving all these sub-strategies to a proper umbrella strategy, that would be very welcome.  
  
And that umbrella strategy can be passed to buffer indeed, and there will be no cyclic dependencies.

> Username: awulkiew  
> Created_at: 2020-04-29 17:00:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417470030  

A disclaimer: I agree that the `IntersectionStrategy` is not the place for all of the getters that currently are there. Below I describe the rationale behind the current state, not claiming that it should stay as it is because I think it should be changed in general. I'm not sure that the `UbrellaStrategy` is the best solution though.  
  
> There should be a proper UbrellaStrategy and this is not the IntersectionStrategy  
  
That would be a breaking change. Note that the `IntersectionStrategy` in this case is at the same time the "original" strategy and also the umbrella strategy containing all strategies needed in algorithms which the `IntersectionStrategy` is passed into since the inclusion of Boost.Geometry into Boost. So it is both kinds of strategy right now, simply because otherwise it'd break the existing code.  
  
> Why should the intersection of two segments define a point_in_polygon?  
  
As above. Because of the design of the library and the desire to not introduce breaking changes.  
  
Consider e.g. set operations, let's say intersection of polygons. The strategy which was always required since the inclusion of Boost.Geometry was the `IntersectionStrategy`. In addition to this the library requires to pass one strategy for an algorithm and it also guarantees that the CS of the strategy "overwrites" the CS of the geometry. So in order to not break the users code we have to require passing the same strategy as usual, in this case the `IntersectionStrategy` and this strategy has to be the umbrella strategy as well because of all of the other strategies needed deeper in the algorithms.  
  
It would be possible to ensure backward compatibility with "proper" `UmbrellaStrategy` though. We'd have to create the `UmbrellaStrategy` from another "original" strategy (e.g. umbrella strategy for set operations created from classically passed intersection strategy). Note however that this means that all strategies in the umbrella strategy would be created based on the original intersection strategy. And this means that the umbrella strategy would be not needed since the library would create all other strategies automatically anyway.  
  
There is also an extensibility issue. What if we introduced a "proper" `UbrellaStrategy` and after a while another lower-level sub-strategy was needed internally in the algorithm? Would we require to pass another sub-strategy into the `UbrellaStrategy` changing the interface and therefore breaking user's code or would we create it automatically based on other strategies that were in the `UmbrellaStrategy` before the change? If the answer is the latter then I'd argue that the `UbrellaStrategy` is not needed in the first place since it should be possible to create any sub-strategy from any set of other sub-strategies.  
  
Btw, I'd be ok with redesigning the strategies part to something different even if this would mean breaking changes. The `UbrellaStrategy` is the most straightforward thing to do. It would cause the problems I described above and probably other which we don't take into account right now. Right now, the `IntersectionStrategy` is partially the `UmbrellaStrategy` so we'd have similar problems with the "proper one". I'm not even sure if we'd solve the cyclic dependency across sub-strategies this way.  
  
Note that all of the problems I mentioned are derived from the original design of the library so I'd consider to do something more radical like e.g. replacing the strategies mechanism with something else instead of adding another layer of strategies for the user to use. I'm talking about the interface here.  
  
Of course I may be mistaken with all of the above so if you have a proposal we can create an Issue and talk about it.

> Username: barendgehrels  
> Created_at: 2020-04-29 17:54:30 UTC  
> Updated_at: 2020-04-29 17:54:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417503575  

Thanks! I will reread this later and react on it, probably this weekend.

> Username: awulkiew  
> Created_at: 2020-04-29 18:59:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417542186  

@barendgehrels After giving it more thought I think we could indeed try with "proper" `UmbrellaStrategy` at least for one function and see how it works out to avoid over-engineering things. I think we could workaround the problems I mentioned above and the result would still be the code which is more clear than it is now (the strategies in particular which I agree should have well-defined purpose).

> Username: barendgehrels  
> Created_at: 2020-05-06 08:41:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r420631581  

I really welcome this idea!  
  
Will we create an issue for it - to get it in our regular process?


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2020-04-22 22:23:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398632394  

📁 include/boost/geometry/strategies/cartesian/turn_in_ring_winding.hpp

```diff
  62 |+         inline counter()
  63 |+             : m_count(0)
  64 |+             , m_min_distance((std::numeric_limits<CalculationType>::max)())
```

> Username: awulkiew  
> Created_at: 2020-04-22 22:23:53 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r413373670  

Can `CalculationType` be user-defined? For user defined types max will be initialized by `T()`. Can this be a problem?

> Username: barendgehrels  
> Created_at: 2020-04-29 09:11:35 UTC  
> Updated_at: 2020-04-29 10:28:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417173413  

Isn't this a problem then everywhere where we use `numeric_limits`?  
But anyway, here it was easy to get rid of it, done  
:heavy_check_mark:

> Username: awulkiew  
> Created_at: 2020-04-29 17:02:46 UTC  
> Updated_at: 2020-04-29 17:02:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#discussion_r417471284  

> Isn't this a problem then everywhere where we use `numeric_limits`?  
  
Yes.


---

## Review 22 [Approved]

> Username: awulkiew  
> Created_at: 2020-04-22 22:31:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/692#pullrequestreview-398635881  

Ok, done. I'm of course ok with this change after addressing the issues I raised. Alternatively you can postpone it or leave it to me because I plan to prepare buffer() to work with cs_undefined geometries anyway and this will require propagating the information about the CS everywhere where it's relevant.

---

## Comment 23

> Username: barendgehrels  
> Created_at: 2020-04-25 11:37:05 UTC  
> Updated_at: 2020-04-29 11:48:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-619365828  

>   
>   
> Ok, done. I'm of course ok with this change after addressing the issues I raised. Alternatively you can postpone it or leave it to me because I plan to prepare buffer() to work with cs_undefined geometries anyway and this will require propagating the information about the CS everywhere where it's relevant.  
  
Hi @awulkiew  thanks for your thorough review. I will address most of the issues you raise on Wednesday, but will probably leave some of the CS related to you then, thanks.  
:heavy_check_mark: All done, pushed, and I will merge. Might there be additional comments, I can always address them in the follow up PR I will create today.

---

## Comment 24

> Username: awulkiew  
> Created_at: 2020-04-29 17:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-621341070  

Ok, thanks for the PR! I raised some additional issues but they are more general and not really related with this PR specifically.

---

## Comment 25

> Username: awulkiew  
> Created_at: 2020-04-29 20:30:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-621446413  

Now when `include/boost/geometry/strategies/cartesian/side_of_intersection.hpp` is removed do we still need the test case `test/strategies/side_of_intersection.cpp`? I commented it out in the Jamfile for now without removing it in case you wanted to still use it for something else.

---

## Comment 26

> Username: barendgehrels  
> Created_at: 2020-05-06 08:42:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/692#issuecomment-624518420  

>   
>   
> Now when `include/boost/geometry/strategies/cartesian/side_of_intersection.hpp` is removed do we still need the test case `test/strategies/side_of_intersection.cpp`? I commented it out in the Jamfile for now without removing it in case you wanted to still use it for something else.  
  
Thanks I will remove it in next PR. Don't need it again.

---
