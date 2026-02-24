# #775 Fix unused parameter warnings [Merged]

> Username: vissarion  
> Created at: 2020-11-13 15:54:53 UTC  
> Updated at: 2020-11-18 13:52:07 UTC  
> Merged at: 2020-11-18 13:52:00 UTC  
> Closed at: 2020-11-18 13:52:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/775  



---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-11-18 12:57:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/775#pullrequestreview-533417862  

📁 include/boost/geometry/strategies/area/cartesian.hpp

```diff
  50 |     static auto get(strategy::area::cartesian<CT> const& strategy)
  51 |     {
  52 |+         boost::ignore_unused(strategy);
```

> Username: awulkiew  
> Created_at: 2020-11-18 12:57:40 UTC  
> Updated_at: 2020-11-18 13:50:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/775#discussion_r526065661  

Here the parameter name could be removed instead, but `ignore_unused` works too. The header is missing though, so maybe it'd be better to simply remove the name.


---

## Review 2 [Approved]

> Username: awulkiew  
> Created_at: 2020-11-18 12:57:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/775#pullrequestreview-533417948  

Thanks!

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-11-18 13:06:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/775#pullrequestreview-533424957  

📁 include/boost/geometry/algorithms/detail/envelope/segment.hpp

```diff
  61 |                              Strategy const& strategy)
  62 |     {
  63 |+         boost::ignore_unused(segment);
```

> Username: awulkiew  
> Created_at: 2020-11-18 13:06:53 UTC  
> Updated_at: 2020-11-18 13:50:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/775#discussion_r526071154  

The header defining `ignore_unused` is missing.


---
