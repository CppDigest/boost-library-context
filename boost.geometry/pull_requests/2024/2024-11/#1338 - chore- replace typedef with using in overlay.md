# #1338 chore: replace typedef with using in overlay [Merged]

> Username: barendgehrels  
> Created at: 2024-11-05 19:18:01 UTC  
> Updated at: 2024-11-13 14:27:06 UTC  
> Merged at: 2024-11-13 14:27:00 UTC  
> Closed at: 2024-11-13 14:27:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1338  



---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-11-11 10:40:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1338#pullrequestreview-2426881517  

LGTM, thanks!

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
1018 |-     //typedef detail::get_turns::get_turn_info_type<Geometry1, Geometry2, AssignPolicy> TurnPolicy;
1017 |+     using turn_policy_t = detail::overlay::get_turn_info<AssignPolicy>;
1018 |+     //using turn_policy_t = detail::get_turns::get_turn_info_type<Geometry1, Geometry2, AssignPolicy>;
```

> Username: vissarion  
> Created_at: 2024-11-11 10:39:43 UTC  
> Updated_at: 2024-11-11 10:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1338#discussion_r1836428421  

Is this alternative policy still needed, if so could you please write a short comment?

> Username: barendgehrels  
> Created_at: 2024-11-11 11:49:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1338#discussion_r1836550477  

Indeed, not needed, I think. I'll check later, and remove (or comment)

> Username: barendgehrels  
> Created_at: 2024-11-13 14:00:54 UTC  
> Updated_at: 2024-11-13 14:01:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1338#discussion_r1840336868  

I investigated - the second (commented) line is the more common and generic way - specializing to the other way anyway for areal/areal.  
~~I will keep the second way.~~  
But it does not compile for some use cases.   
I probably add a comment then.


---
