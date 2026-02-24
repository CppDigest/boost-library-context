# #1007 [strategies] make cartesian_winding similar to spherical_winding [Merged]

> Username: barendgehrels  
> Created at: 2022-05-22 10:21:28 UTC  
> Updated at: 2022-06-01 09:10:56 UTC  
> Merged at: 2022-06-01 09:10:50 UTC  
> Closed at: 2022-06-01 09:10:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1007  

This prepares a next phase where this will be used from `turn_in_ring_winding`

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-05-23 09:48:15 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1007#pullrequestreview-981470852  

Looks good to me.

📁 include/boost/geometry/strategies/cartesian/point_in_poly_winding.hpp

```diff
 240 |+ >
 241 |+ class cartesian_winding
 242 |+   : public detail::cartesian_winding_base<typename side::services::default_strategy<cartesian_tag, CalculationType>::type, CalculationType> {};
```

> Username: vissarion  
> Created_at: 2022-05-23 09:47:53 UTC  
> Updated_at: 2022-05-23 09:48:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1007#discussion_r879249009  

isn't it too long? If not skip my comment.

> Username: barendgehrels  
> Created_at: 2022-05-29 19:30:27 UTC  
> Updated_at: 2022-05-29 19:30:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1007#discussion_r884314652  

It is indeed. Will fix.

> Username: barendgehrels  
> Created_at: 2022-06-01 08:32:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1007#discussion_r886536368  

:heavy_check_mark:


---
