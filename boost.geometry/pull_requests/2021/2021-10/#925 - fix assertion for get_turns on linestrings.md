# #925 [get_turns] fix assertion for get_turns on linestrings [Merged]

> Username: barendgehrels  
> Created at: 2021-10-20 09:37:42 UTC  
> Updated at: 2021-10-26 15:33:18 UTC  
> Merged at: 2021-10-26 15:33:02 UTC  
> Closed at: 2021-10-26 15:33:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/925  

Fixes #896

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-10-20 09:38:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/925#pullrequestreview-784240292  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 254 |-         auto const dm
 245 |+         bool const p_in_range = index_p < range_p.size();
 246 |+         bool const q_in_range = index_q < range_q.size();
```

> Username: barendgehrels  
> Created_at: 2021-10-20 09:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/925#discussion_r732593394  

This is the fix (testing it), the rest are some layout changes


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-10-26 15:33:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/925#issuecomment-952059716  

Merged, I assume it's fine

---
