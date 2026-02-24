# #412 Geographic coordinate system related fixes. [Merged]

> Username: awulkiew  
> Created at: 2017-07-21 01:49:50 UTC  
> Updated at: 2017-08-30 12:25:51 UTC  
> Merged at: 2017-07-31 16:30:26 UTC  
> Closed at: 2017-07-31 16:30:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/412  

Changes done in this PR:  
 - passing of side strategy into internals of various algorithms (e.g. `buffer`, `is_simple`, `is_valid`, `remove_spikes`).  
 - support for user-defined strategy in `is_convex`.  
 - fix for spike detection:  
   - removed redundant code in `point_is_spike_or_equal`, replaced with `direction_code`  
   - in some edge cases of very small coordinates differences function was returning invalid results, so now instead of checking both axes, the axis with greater difference determines the direction  
   - `direction_code` is dispatched by coordinate system and calculated differently for non-cartesian points  
- fix for sectionalize and get_turns, handle longitudes differently in:  
  - section functions `preceding`, `exceeding` (used in get_turns and buffer)  
  - segment direction calculation in sectionalize

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2017-07-21 02:01:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/412#pullrequestreview-51372186  

📁 include/boost/geometry/algorithms/detail/point_is_spike_or_equal.hpp

```diff
  84 |+         bool r2 = direction_code(segment_a, segment_b, last_point) < 1;
  85 |+         if (r1 != r2)
  86 |+             std::cout << "spike detection failure with: " << r1 << " " << r2 << std::endl;
```

> Username: awulkiew  
> Created_at: 2017-07-21 02:01:04 UTC  
> Updated_at: 2017-07-27 01:40:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/412#discussion_r128669897  

Temporarily I added code comparing the previous and current version of spike detection enabled when defined `BOOST_GEOMETRY_ENABLE_POINT_IS_SPIKE_OR_EQUAL_TEST`. In case you wanted to check what's the difference, i.e. where the result was wrong. I noticed several differences in some buffer tests (AFAIR multi_polygon), union and more.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-07-27 01:54:19 UTC  
> Updated_at: 2017-07-27 13:53:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/412#issuecomment-318235525  

I fixed more bugs related to the wrong handling of non-cartesian CS in get_turns/sectionalize, namely special handling of longitudes (wrapping) in `preceding`/`exceeding` functions and sectionalize segment direction calculation.  
  
There is still one problematic thing though. In sectionalize the segment direction is calculated and while for longitudes it's possible to calculate it for geographic segments, for latitudes it probably is not possible because geographic segment may e.g. go first north and then south. Bounding box of such segment may have both min and max latitude both below and above the first segment's endpoint. For now it's ok because directions in dimension greater than 0 are used only in `buffer` and buffer doesn't support non-cartesian geometries anyway but in the future we should think about a replacement. Assuming my understanding is correct, i.e. it's used in `buffer` in similar way to `get_turns`.

---
