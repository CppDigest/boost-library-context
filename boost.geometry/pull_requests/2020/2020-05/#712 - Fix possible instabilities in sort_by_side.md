# #712 [overlay] Fix possible instabilities in sort_by_side [Merged]

> Username: barendgehrels  
> Created at: 2020-05-13 12:39:49 UTC  
> Updated at: 2020-05-15 20:45:18 UTC  
> Merged at: 2020-05-15 20:45:15 UTC  
> Closed at: 2020-05-15 20:45:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/712  

* caused a crash for non-rescaled code (currently protected by TEST_FAILURE)  
* it made the `std::sort`, where it was used, instable and output rubbish (alternatively we could use `stable_sort`)  
* plus some minor code refactoring

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-05-13 12:42:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/712#pullrequestreview-410882864  

📁 include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp

```diff
 188 | 
 189 |-         int const side_first_wrt_second = -side_second_wrt_first;
 189 |+         int const side_first_wrt_second = m_strategy.apply(m_turn_point, second.point, first.point);
```

> Username: barendgehrels  
> Created_at: 2020-05-13 12:42:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/712#discussion_r424405685  

This block is the fix itself


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2020-05-13 13:48:23 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/712#pullrequestreview-410942543  

thanks! ok for me

---
