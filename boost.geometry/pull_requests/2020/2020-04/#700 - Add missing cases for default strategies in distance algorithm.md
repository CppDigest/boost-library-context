# #700 Add missing cases for default strategies in distance algorithm [Merged]

> Username: vissarion  
> Created at: 2020-04-22 11:04:32 UTC  
> Updated at: 2021-06-30 14:05:41 UTC  
> Merged at: 2020-07-08 18:44:22 UTC  
> Closed at: 2020-07-08 18:44:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/700  

This PR fixes an issue on default strategy selection for distance algorithm and affects all coordinate systems.   
  
The default strategy for the cases `polygonal/box` and `linear/box` should be a segment/box strategy and not a point/segment one.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-06 16:19:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/700#pullrequestreview-443223455  

📁 include/boost/geometry/algorithms/detail/distance/default_strategies.hpp

```diff
 119 |-               typename point_type<Box>::type
 120 |-           >
 126 |+     : default_strategy_polygonal_or_linear<Linear, Box>
```

> Username: awulkiew  
> Created_at: 2020-07-06 16:19:47 UTC  
> Updated_at: 2020-07-06 16:19:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/700#discussion_r450334850  

Doesn't it cause some cycle of base classes?  
  
`default_strategy<segment, box>` is derived from `default_strategy_polygonal_or_linear` which is derived from `default_strategy<segment, box>` etc.  
  
My gut feeling tells me that this particular specialization should define a strategy directly with `typedef`. Or am I missing something?

> Username: awulkiew  
> Created_at: 2020-07-08 18:43:39 UTC  
> Updated_at: 2020-07-08 18:45:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/700#discussion_r451753088  

What I was missing is that this `default_strategy` is in the `namespace detail::distance`.


---
