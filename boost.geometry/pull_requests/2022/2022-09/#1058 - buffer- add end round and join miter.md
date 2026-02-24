# #1058 buffer: add end round and join miter [Merged]

> Username: barendgehrels  
> Created at: 2022-09-21 12:46:06 UTC  
> Updated at: 2022-11-17 15:08:17 UTC  
> Merged at: 2022-10-12 09:59:48 UTC  
> Closed at: 2022-10-12 09:59:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058  



---

## Comment 1

> Username: barendgehrels  
> Created_at: 2022-10-12 08:00:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#issuecomment-1275751159  

Can this be reviewed? @vissarion @awulkiew ?

---

## Comment 2

> Username: vissarion  
> Created_at: 2022-10-12 08:04:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#issuecomment-1275756523  

> Can this be reviewed? @vissarion @awulkiew ?  
  
Sorry for the delays, I had started but left it incomplete, will finish it today.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2022-10-12 09:26:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1058#pullrequestreview-1138702737  

Thanks! I am OK with merging. There is an issue about robustness when different inverse/direct formulas are used but that could be examined/addressed after merging.

📁 include/boost/geometry/strategies/geographic/buffer_join_miter.hpp

```diff
  35 |+ class geographic_join_miter
  36 |+ {
  37 |+     static bool const enable_azimuth = true;
```

> Username: vissarion  
> Created_at: 2022-10-12 08:52:36 UTC  
> Updated_at: 2022-10-12 09:26:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#discussion_r993185707  

this part is exactly the same as in `geographic_end_round`. Does it make sense to define it only once and reuse it.


📁 include/boost/geometry/strategies/geographic/buffer_end_round.hpp

```diff
  61 |+     template <typename T, typename RangeOut>
  62 |+     inline void generate(T lon_rad, T lat_rad,
  63 |+                 T distance, T azimuth,
```

> Username: vissarion  
> Created_at: 2022-10-12 08:53:14 UTC  
> Updated_at: 2022-10-12 09:26:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#discussion_r993186456  

indentation looks weird here

---

📁 include/boost/geometry/strategies/geographic/buffer_end_round.hpp

```diff
  85 |+     template <typename Point, typename RangeOut, typename DistanceStrategy>
  86 |+     inline void apply(Point const& penultimate_point,
  87 |+                 Point const& perp_left_point,
```

> Username: vissarion  
> Created_at: 2022-10-12 08:53:30 UTC  
> Updated_at: 2022-10-12 09:26:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#discussion_r993186764  

indentation issue


📁 include/boost/geometry/strategies/geographic/buffer_join_round.hpp

```diff
  47 |+         <
  48 |+             T, enable_coordinates, false, false, false
  49 |+         >;
```

> Username: vissarion  
> Created_at: 2022-10-12 09:17:54 UTC  
> Updated_at: 2022-10-12 09:26:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1058#discussion_r993212860  

please see comment above


---
