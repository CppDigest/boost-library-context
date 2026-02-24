# #605 Define CS-specific behavior of algorithms only with strategy (part 2). [Merged]

> Username: awulkiew  
> Created at: 2019-06-24 10:32:43 UTC  
> Updated at: 2019-06-30 15:27:09 UTC  
> Merged at: 2019-06-30 15:27:09 UTC  
> Closed at: 2019-06-30 15:27:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/605  

Followup of https://github.com/boostorg/geometry/pull/533  
  
The changes:  
- rtree may take a strategy as `bgi::parameters<RtreeParams, Strategy>`, not all possible operations are supported, only those required by other algorithms where the rtree is used, for other operations the default strategy is used as it was before. This needs further changes but I think it needs more general changes in strategies, i.e. introduction of "proper" umbrella strategies.  
- rescale_policy_type takes optional CSTag (previously CS-related behavior was defined by Geometry type).  
- the information about the CS taken from the strategy is propagated internally in various algorithms.  
- Point types are now optional in point_in_poly strategies (left as template arguments defaulted to void for backward compatibility).  
- getters for CS-specific strategies added to some strategies.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-06-27 14:06:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/605#pullrequestreview-255247519  

Thanks for this PR. I am ok with merging.

📁 include/boost/geometry/algorithms/detail/is_valid/polygon.hpp

```diff
 478 | 
 456 |-         typedef has_valid_self_turns<Polygon> has_valid_turns;
 479 |+         typedef has_valid_self_turns<Polygon, typename Strategy::cs_tag> has_valid_turns;
```

> Username: vissarion  
> Created_at: 2019-06-27 14:01:02 UTC  
> Updated_at: 2019-06-29 19:06:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/605#discussion_r298193042  

why not `typedef`ed as above or not `typedef` the above to look alike. Similar cases below. Am I missing something?

> Username: awulkiew  
> Created_at: 2019-06-27 15:15:53 UTC  
> Updated_at: 2019-06-29 19:06:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/605#discussion_r298231030  

No, there is no specific cause. I think I prefer removing `typedef` above because it's used only in one place.

> Username: barendgehrels  
> Created_at: 2019-06-28 10:10:03 UTC  
> Updated_at: 2019-06-29 19:06:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/605#discussion_r298536233  

Thanks! It is huge, and I could not check all details, but what I've seen looks to me. So I'm OK with merging this.

> Username: awulkiew  
> Created_at: 2019-06-28 12:03:17 UTC  
> Updated_at: 2019-06-29 19:06:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/605#discussion_r298565424  

Thanks, I think I'll also add some tests for geometries with undefined CS before merging.


---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-06-28 21:39:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/605#issuecomment-506885247  

I addressed the issue raised by @vissarion, resolved the conflict with develop, added tests and fixed one more issue, i.e. passed the strategy into `get_rescale_policy()` which calls `envelope()` and `expand()`. I plan to merge it this weekend.

---
