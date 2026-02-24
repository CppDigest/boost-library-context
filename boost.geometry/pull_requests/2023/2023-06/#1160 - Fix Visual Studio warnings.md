# #1160 Fix Visual Studio warnings [Merged]

> Username: awulkiew  
> Created at: 2023-06-07 20:30:11 UTC  
> Updated at: 2023-07-02 16:17:05 UTC  
> Merged at: 2023-07-01 16:10:43 UTC  
> Closed at: 2023-07-01 16:10:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160  

- C4100 Unreferenced formal parameter  
- C4127 Conditional expression is constant  
- C4456 Declaration hides previous local declaration  
- C4701 Potentially uninitialized local variable used  
- C4702 Unreachable code

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2023-06-07 20:33:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1468517623  

📁 include/boost/geometry/algorithms/detail/overlay/pointlike_pointlike.hpp

```diff
 259 |-         if ( OverlayType != overlay_difference
 260 |-              && boost::size(multipoint1) > boost::size(multipoint2) )
 262 |+         if (BOOST_GEOMETRY_CONDITION(
```

> Username: awulkiew  
> Created_at: 2023-06-07 20:33:30 UTC  
> Updated_at: 2023-06-07 20:33:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1222126477  

What do you think about adding a macro allowing to conditionally use C++17 `if constexpr`?

> Username: vissarion  
> Created_at: 2023-06-08 07:56:49 UTC  
> Updated_at: 2023-06-08 08:01:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1222604530  

I am OK with it.

> Username: barendgehrels  
> Created_at: 2023-06-10 11:18:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225296477  

It can probably be integrated into `BOOST_GEOMETRY_CONDITION` ? Such that we don't need another one?

> Username: awulkiew  
> Created_at: 2023-07-01 13:51:49 UTC  
> Updated_at: 2023-07-01 13:51:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1248847879  

@barendgehrels Unfortunately we do need two separate macros. AFAIR there are rare places where a condition can be constant or not, e.g. some policy can define a variable as static or dynamic member. Of course we could agree to drop static members in such cases.  
  
Another thing is that I think we should consider something which looks more like the language feature. I'd also prefer to keep `if` so to not include it in the macro in order to allow the editor to highlight the key word to make it more readable. So maybe something like this:  
```  
if constexpr (OverlayType != overlay_difference)  
vs  
if BOOST_GEOMETRY_CONSTEXPR (OverlayType != overlay_difference)  
```

> Username: awulkiew  
> Created_at: 2023-07-01 15:35:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1248874514  

See the condition below in pj_transform.hpp line 255:  
```  
if( BOOST_GEOMETRY_CONDITION(srcdefn.vto_meter != 1.0 && dimension > 2) )  
```  
where `srcdefn.vto_meter` may or may not be static but `dimension` is always static. In this case `if constexpr` should be used for `dimension` and `BOOST_GEOMETRY_CONDITION` for `srcdefn.vto_meter`. Or `vto_meter` should always be non-static member, also in static projections .

> Username: barendgehrels  
> Created_at: 2023-07-02 16:17:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1249605754  

OK, I see, and saw it in your new PR this morning. Cool.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2023-06-07 20:35:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1468520634  

📁 include/boost/geometry/strategies/cartesian/turn_in_ring_winding.hpp

```diff
 115 |-                              counter& the_state,
 116 |-                              Ring const& full_ring)
 116 |+                              counter& the_state)
```

> Username: awulkiew  
> Created_at: 2023-06-07 20:35:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1222128077  

@barendgehrels Is that ok or should I rather leave the parameter and remove the name?

> Username: barendgehrels  
> Created_at: 2023-06-10 11:22:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225298368  

It's OK, thanks. I also answered elsewhere before seeing this comment.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2023-06-08 08:01:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1469243455  

Thanks!

---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2023-06-10 11:16:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1473391694  

📁 include/boost/geometry/algorithms/detail/buffer/piece_border.hpp

```diff
 355 |     {
 356 |-         return strategy.apply(point, p1, p2, place_on_ring, m_is_convex, state, get_full_ring());
 356 |+         return strategy.apply(point, p1, p2, place_on_ring, m_is_convex, state);
```

> Username: barendgehrels  
> Created_at: 2023-06-10 11:16:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225295646  

Hmm, this was probably a left over, only used for debugging.  
Deletion looks good to me, thanks.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2023-06-10 11:20:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1473392213  

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 485 |             std::size_t rot_index = index;
 485 |-             if (is_closed_in && size > 1)
 486 |+             if (BOOST_GEOMETRY_CONDITION(is_closed_in && size > 1))
```

> Username: barendgehrels  
> Created_at: 2023-06-10 11:20:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225297517  

Related to `if constexpr`, isn't it, in cases like this, better to only apply the const part on the part being const?  
I mean: `if (BOOST_GEOMETRY_CONDITION(is_closed_in) && size > 1)` ?

> Username: awulkiew  
> Created_at: 2023-07-01 16:01:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1248884622  

done


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2023-06-10 11:20:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1473392244  

📁 include/boost/geometry/algorithms/simplify.hpp

```diff
 500 |             // Open output if needed
 500 |-             if (! is_closed_out && boost::size(out) > 1)
 501 |+             if (BOOST_GEOMETRY_CONDITION(! is_closed_out && boost::size(out) > 1))
```

> Username: barendgehrels  
> Created_at: 2023-06-10 11:20:52 UTC  
> Updated_at: 2023-06-10 11:20:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225297676  

Same here


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2023-06-10 11:21:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1473392269  

📁 include/boost/geometry/formulas/area_formulas.hpp

```diff
 383 | 
 383 |-         if (LongSegment && lat1r != lat2r) // not for segments parallel to equator
 384 |+         if (BOOST_GEOMETRY_CONDITION(LongSegment && lat1r != lat2r)) // not for segments parallel to equator
```

> Username: barendgehrels  
> Created_at: 2023-06-10 11:21:02 UTC  
> Updated_at: 2023-06-10 11:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#discussion_r1225297775  

And here, and more.


---

## Review 8 [Approved]

> Username: barendgehrels  
> Created_at: 2023-06-10 11:23:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1160#pullrequestreview-1473392563  

Looks good to me in general, the only thing is that I would like to separate the parts really being const from the parts that might vary.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2023-07-01 16:02:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1160#issuecomment-1615975203  

Thanks. I separated them.

---
