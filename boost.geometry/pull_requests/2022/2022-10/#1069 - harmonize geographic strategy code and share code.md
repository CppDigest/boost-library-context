# #1069 [buffer] harmonize geographic strategy code and share code [Merged]

> Username: barendgehrels  
> Created at: 2022-10-19 09:47:22 UTC  
> Updated at: 2022-11-17 15:04:51 UTC  
> Merged at: 2022-11-09 10:46:35 UTC  
> Closed at: 2022-11-09 10:46:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069  

As discussed in last PR

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-10-19 09:49:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1069#pullrequestreview-1147221719  

📁 include/boost/geometry/strategies/geographic/buffer_side_straight.hpp

```diff
 119 |-         set_from_radian<1>(output_range.back(), d2.lat2);
  94 |+         helper::append_point(lon1_rad, lat1_rad, distance, angle, m_spheroid, range_out);
  95 |+         helper::append_point(lon2_rad, lat2_rad, distance, angle, m_spheroid, range_out);
```

> Username: barendgehrels  
> Created_at: 2022-10-19 09:49:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r999208549  

Especially in this one, it results in much cleaner code.  
  
The output range is not resized here, but it always starts empty, so the code is effectively the same.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-10-21 15:03:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1069#pullrequestreview-1151117272  

📁 include/boost/geometry/strategies/geographic/buffer_join_round.hpp

```diff
  73 |         }
  74 | 
  75 |+         static calc_t const two_pi = geometry::math::two_pi<calc_t>();
```

> Username: awulkiew  
> Created_at: 2022-10-21 15:03:52 UTC  
> Updated_at: 2022-10-21 15:38:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1001896073  

This is probably out of scope of this PR but `static` variables could be replaced with `constexpr` variables. For this to work math constants would have to be `constexpr` functions.

> Username: barendgehrels  
> Created_at: 2022-10-22 09:28:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1002428374  

Indeed, I'll do that later in a separate PR


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2022-10-21 15:05:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1069#pullrequestreview-1151119959  

📁 include/boost/geometry/strategies/geographic/buffer_side_straight.hpp

```diff
  56 |         typename Point,
  55 |-         typename OutputRange,
  57 |+         typename RangeOut,
```

> Username: awulkiew  
> Created_at: 2022-10-21 15:05:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1001897775  

Is there a reason why template arguments are in different order than function arguments?

> Username: barendgehrels  
> Created_at: 2022-10-22 09:29:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1002428392  

Thanks, no, I'll update it.

> Username: barendgehrels  
> Created_at: 2022-11-09 08:41:07 UTC  
> Updated_at: 2022-11-09 08:41:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1017624437  

:heavy_check_mark:


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2022-10-21 16:26:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1069#pullrequestreview-1151236817  

📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
  65 |+     //! (if points_per_circle is smaller than 4, it is internally set to 4)
  66 |+     explicit geographic_point_circle(std::size_t points_per_circle = 90)
  67 |+         : m_points_per_circle((points_per_circle < 4u) ? 4u : points_per_circle)
```

> Username: vissarion  
> Created_at: 2022-10-21 16:23:51 UTC  
> Updated_at: 2022-10-21 16:26:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1001977055  

what is the point of changing this from 3 to 4? Is this for robustness?

> Username: barendgehrels  
> Created_at: 2022-10-22 09:30:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1002428520  

One had 3, the other had 4. I liked to harmonize it. I can make both `3` too. Either choice is fine, it's very low anyway, not really circle-like.

> Username: barendgehrels  
> Created_at: 2022-11-09 08:42:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1017626246  

In hindsight, if users use this 3 already, it changes behavior for them. So I put it back to `3` (for circle) and kept `4` (for joins/ends) and harmonized it among the strategies.  
Thanks for your remark.

> Username: barendgehrels  
> Created_at: 2022-11-09 10:48:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1069#discussion_r1017775183  

For the join it was also 3 but that was erroneous, I made it minimum 4 to get well-shaped symmetric joins.  
I don't think it was ever used with 3 (default = 90)


---
