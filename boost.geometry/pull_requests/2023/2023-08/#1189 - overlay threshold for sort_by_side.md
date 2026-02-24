# #1189 [fix] overlay threshold for sort_by_side [Merged]

> Username: barendgehrels  
> Created at: 2023-08-23 16:34:21 UTC  
> Updated at: 2023-12-04 10:43:46 UTC  
> Merged at: 2023-09-13 18:29:38 UTC  
> Closed at: 2023-09-13 18:29:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1189  

Fixes: issue #1186

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2023-09-13 09:28:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1189#pullrequestreview-1624103432  

Thanks!   
  
It would help a lot if you comment on the geometric intuition of the value 100 that is used as threshold.  
  
Regarding https://github.com/boostorg/geometry/issues/1183 is there some other combination of values that fixes that or you believe it is of different nature?

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 327 | 
 328 |-         ct_type const tolerance = 1000000000;
 328 |+         static auto const tolerance = common_approximately_equals_epsilon<ct_type>::value();
```

> Username: vissarion  
> Created_at: 2023-09-13 09:26:15 UTC  
> Updated_at: 2023-09-13 09:28:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1189#discussion_r1324238044  

this is the actual change right?

> Username: barendgehrels  
> Created_at: 2023-09-13 18:09:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1189#discussion_r1324890594  

Indeed!

> Username: barendgehrels  
> Created_at: 2023-09-13 18:14:51 UTC  
> Updated_at: 2023-09-13 18:14:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1189#discussion_r1324896785  

I'll document the `100` later. It's not new, it's moved.   
It is recently (earlier this year) adapted from a larger value to this value, considering another issue and all current tests.  
It is used for clustering turns, and now also for sort by side. In both cases the exact value is not that important, but it should not be 1 (for floating point comparisons) and not be too large (obviously). The tolerance which was now replaced was very large, I don't remember exactly why.

> Username: barendgehrels  
> Created_at: 2023-09-13 20:18:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1189#discussion_r1325035574  

See also #1108


---
