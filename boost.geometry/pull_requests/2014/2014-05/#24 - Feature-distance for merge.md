# #24 Feature/distance for merge [Merged]

> Username: mkaravel  
> Created at: 2014-05-08 14:41:03 UTC  
> Updated at: 2014-05-14 10:35:59 UTC  
> Merged at: 2014-05-14 10:35:59 UTC  
> Closed at: 2014-05-14 10:35:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/24  

Implementation of:  
  
```  
distance(g1, g2)  
distance(g1, g2, strategy)  
comparable_distance(g1, g2)  
comparable_distance(g1, g2, strategy)  
```  
  
for all geometry combinations

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-05-08 21:08:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/24#issuecomment-42606499  

Re-wrote the implementation of detail::distance::default_strategy so that it does reverse_dispatch on the geometries automatically.  
  
Using the above modifications, re-wrote/simplified the implementations for   
  
```  
distance(g1, g2)  
comparable_distance((g1, g2)  
```  
  
with respect to how the default strategy is defined, and re-implemented the default_distance_result using detail::distance::default_strategy.  
  
Added a prototype implementation (not yet used) of default_comparable_distance_result.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-05-09 20:09:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/24#issuecomment-42708162  

The last three commits above correspond to removing the  
  
```  
strategy::distance::services::strategy_point_point  
```  
  
metafunction

---
