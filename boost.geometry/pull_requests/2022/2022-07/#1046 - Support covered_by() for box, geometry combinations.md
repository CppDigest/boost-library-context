# #1046 Support covered_by() for box, geometry combinations [Merged]

> Username: vissarion  
> Created at: 2022-07-28 13:41:14 UTC  
> Updated at: 2022-11-17 15:05:05 UTC  
> Merged at: 2022-10-13 11:40:33 UTC  
> Closed at: 2022-10-13 11:40:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046  

This PR implements support for `covered_by()` for the following combinations of a box and a geometry:  
- `convered_by(box, areal)` (only cartesian CS)   
- `convered_by(geometry, box)`  
  
The first case resolves issue https://github.com/boostorg/geometry/issues/604

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-08-04 09:31:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1046#pullrequestreview-1061580165  

📁 include/boost/geometry/algorithms/detail/covered_by/implementation.hpp

```diff
  71 |+         box_type box_areal;
  72 |+         geometry::envelope(geometry, box_areal, strategy);
  73 |+         return strategy.covered_by(box_areal, box).apply(box_areal, box);
```

> Username: awulkiew  
> Created_at: 2022-08-04 09:31:49 UTC  
> Updated_at: 2022-08-04 09:34:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r937563636  

This approach while giving the correct result is suboptimal because it cannot be interrupted  if e.g. one of the points/segments of a geometry is outside the box. The whole envelope is calculated each time. If that's acceptable then ok. But if it isn't but for whatever reason you decide to not implement this now maybe you could put TODO here and create another issue about this.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-08-04 09:36:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1046#pullrequestreview-1061586369  

📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  36 |+                 typename traits::coordinate_system<typename traits::point_type<Box>::type>::type,
  37 |+                 cs::cartesian
  38 |+             >;
```

> Username: awulkiew  
> Created_at: 2022-08-04 09:36:28 UTC  
> Updated_at: 2022-08-04 09:44:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r937568117  

You should probably use `Strategy::cs_tag` to distinguish between CSes. It's because the CS of a geometry may be undefined and it's the Strategy that defines CS-related operations.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-08-04 09:37:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1046#pullrequestreview-1061588342  

📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  59 |+         BOOST_GEOMETRY_STATIC_ASSERT_FALSE(
  60 |+             "Not implemented for this coordinate system.",
  61 |+             typename traits::coordinate_system<typename traits::point_type<Box>::type>::type);
```

> Username: awulkiew  
> Created_at: 2022-08-04 09:37:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r937569437  

Is this a breaking change? AFAIU previously box was silently converted to ring in non-cartesian CSes, correct?

> Username: vissarion  
> Created_at: 2022-08-30 14:29:49 UTC  
> Updated_at: 2022-08-30 14:29:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r958559446  

I think we are OK since this operation was not supported in the past, i.e. `covered_by(box, polygon)` do not compile in any  cs before this PR.


---

## Comment 4

> Username: vissarion  
> Created_at: 2022-08-30 14:31:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#issuecomment-1231751647  

@awulkiew thanks for your comments I addressed them by pushing new commits.

---

## Comment 5

> Username: vissarion  
> Created_at: 2022-10-12 09:33:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#issuecomment-1275871201  

@barendgehrels are you OK with this PR?

---

## Review 6 [Approved]

> Username: barendgehrels  
> Created_at: 2022-10-12 10:09:09 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1046#pullrequestreview-1059987192  

I made some comments, still pending, but basically I'm OK. Thanks!

📁 include/boost/geometry/algorithms/detail/covered_by/implementation.hpp

```diff
  71 |+         box_type box_areal;
  72 |+         geometry::envelope(geometry, box_areal, strategy);
  73 |+         return strategy.covered_by(box_areal, box).apply(box_areal, box);
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:28:29 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936444291  

Is this sufficient for the generic case? It looks surprisingly simple.  
  
But probably yes.  
![image](https://user-images.githubusercontent.com/334849/182574661-0f081077-e0f3-480a-b522-5496ed5745b9.png)

> Username: barendgehrels  
> Created_at: 2022-08-03 09:31:09 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936446888  

~~Fox box/box , calculating the envelope is actually not necessary.  
For box/point neither.  
Maybe the replaced code should still stay, and this new case to support other combinations.~~  
  
You covered that already below, fine.

---

📁 include/boost/geometry/algorithms/detail/covered_by/implementation.hpp

```diff
  75 |- };
  68 |+         using point_type = typename point_type<Geometry>::type;
  69 |+         using box_type = model::box<point_type>;
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:29:50 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936445573  

We probably should use a helper geometry here, in case point_type is not mutable


📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  48 |+         using box_view = boost::geometry::box_view<Box>;
  49 |+         box_view view(box);
  50 |+         areal_areal<decltype(view), Areal>::apply(view, areal, result, strategy);
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:47:57 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936463972  

Why not just `areal_areal<box_view, Areal>`?

---

📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  34 |+         using is_cartesian = std::is_same
  35 |+             <
  36 |+                 typename traits::coordinate_system<typename traits::point_type<Box>::type>::type,
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:50:55 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936466816  

Can you remove `traits::` ? It's meant to implement a point type, but not to be used in normal code

---

📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  29 |+     template <typename Result, typename Strategy>
  30 |+     static inline void apply(Box const& box, Areal const& areal,
  31 |+                              Result & result,
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:51:32 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936467383  

-> `Result&`

---

📁 include/boost/geometry/algorithms/detail/relate/box_areal.hpp

```diff
  59 |+         BOOST_GEOMETRY_STATIC_ASSERT_FALSE(
  60 |+             "Not implemented for this coordinate system.",
  61 |+             typename traits::coordinate_system<typename traits::point_type<Box>::type>::type);
```

> Username: barendgehrels  
> Created_at: 2022-08-03 09:52:56 UTC  
> Updated_at: 2022-10-12 10:09:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#discussion_r936468806  

Here too, no `traits::`


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2022-10-12 10:10:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#issuecomment-1275922346  

> @barendgehrels are you OK with this PR?  
  
See my review remark, basically yes, but can you look at my comments I made earlier?

---

## Comment 8

> Username: vissarion  
> Created_at: 2022-10-13 08:12:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1046#issuecomment-1277212401  

> > @barendgehrels are you OK with this PR?  
>   
> See my review remark, basically yes, but can you look at my comments I made earlier?  
  
Thanks for the review! Somehow your comments appeared to me yesterday. I addressed them and now I can merge this PR.

---
