# #767 Modernize tuple-related code. [Merged]

> Username: awulkiew  
> Created at: 2020-10-19 00:30:57 UTC  
> Updated at: 2020-10-21 14:34:04 UTC  
> Merged at: 2020-10-21 14:34:04 UTC  
> Closed at: 2020-10-21 14:34:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/767  

- replace `boost::tuple` with `std::tuple` in some places  
- replace `policies::relate::segments_tupled` with `policies::relate::segments_intersection_policy` and instead of `get<I>` to access underlying policies (`policies::relate::segments_intersection_points` and `policies::relate::segments_direction`) use clearly named members `intersection_points` and `direction`. So something like this: `result.template get<1>()` becomes this: `result.direction`, which is more clear.  
- replace `boost::tuple` with custom, lightweight tuple in srs projections/transformations which decreases compilation time  
- modernize our tuple-related utilities defined in ` include/boost/geometry/util/tuples.hpp`

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-10-20 07:46:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/767#pullrequestreview-512420147  

Looks OK to me, thanks!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2020-10-21 08:28:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/767#pullrequestreview-513423539  

I glanced through most of it, looks OK to me in general. Thanks.

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_for_endpoint.hpp

```diff
 122 |+         int arrival_b = result.direction.arrival[1];
 123 |+         bool same_dirs = result.direction.dir_a == 0
 124 |+                       && result.direction.dir_b == 0;
```

> Username: barendgehrels  
> Created_at: 2020-10-21 08:22:23 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509083390  

Much more readable, thanks


📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info_helpers.hpp

```diff
 443 |-     typedef typename boost::tuples::element<1, result_type>::type d_info_type; // dir_info
 432 |+     typedef typename result_type::intersection_points_type i_info_type; // intersection_info
 433 |+     typedef typename result_type::direction_type d_info_type;           // dir_info
```

> Username: barendgehrels  
> Created_at: 2020-10-21 08:23:29 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509084154  

The comments can be omitted now, the types are clear enough.

> Username: awulkiew  
> Created_at: 2020-10-21 11:56:17 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509216290  

Right.


📁 include/boost/geometry/policies/relate/intersection_policy.hpp

```diff
   1 |+ // Boost.Geometry (aka GGL, Generic Geometry Library)
   2 |+ 
   3 |+ // Copyright (c) 2007-2012 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: barendgehrels  
> Created_at: 2020-10-21 08:24:33 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509084911  

You can make it 2020

> Username: awulkiew  
> Created_at: 2020-10-21 14:21:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509330805  

Done.

---

📁 include/boost/geometry/policies/relate/intersection_policy.hpp

```diff
 134 |+ 
 135 |+ // "tupled" to return intersection results together.
 136 |+ // NOTE: unused, left for backward compatibility
```

> Username: barendgehrels  
> Created_at: 2020-10-21 08:26:22 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509086250  

Is it unused? I think it's still used.  
If not used at all, I've no objection to remove it.

> Username: awulkiew  
> Created_at: 2020-10-21 14:21:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509330664  

I removed it.


📁 include/boost/geometry/srs/projections/spar.hpp

```diff
  67 |-     > type;
  54 |+     BOOST_GEOMETRY_STATIC_ASSERT_FALSE(
  55 |+         "I out of bounds.",
```

> Username: barendgehrels  
> Created_at: 2020-10-21 08:27:43 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509087192  

Can this be a `std` static assert? But it's TODO anyway so you can leave it for now

> Username: awulkiew  
> Created_at: 2020-10-21 11:56:00 UTC  
> Updated_at: 2020-10-21 14:21:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#discussion_r509216123  

It is std `static_assert` which fails when this template is instantiated. This is a utility I added recently which allows to do this. Note that raw `static_assert(false)` would fail always when the class is compiled, even if the template is not instantiated.  
  
The TODO comment is only about making it a metafunction and moving to util/sequence.hpp in the future.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-10-21 14:22:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/767#issuecomment-713613831  

Thanks for the reviews.

---
