# #1301 feat: deprecate duplicate unused strategy distance_projected_point_ax [Merged]

> Username: barendgehrels  
> Created at: 2024-08-06 12:17:41 UTC  
> Updated at: 2024-08-20 19:57:32 UTC  
> Merged at: 2024-08-20 19:57:26 UTC  
> Closed at: 2024-08-20 19:57:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1301  

This deprecates `distance_projected_point_ax`  
  
* it is not used inside the library  
* it is a remainder of moving the strategy to `projected_point`  
* it therefore contained duplicate point  
* it is not documented and most probably not used by anyone  
* this happened in 2014, most probably

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2024-08-09 12:45:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1301#pullrequestreview-2230100335  

Thanks, this looks redundant indeed.

📁 include/boost/geometry/strategies/cartesian/distance_projected_point_ax.hpp

```diff
  22 | 
  23 |+ #include <boost/config/pragma_message.hpp>
  24 |+ BOOST_PRAGMA_MESSAGE("This include file is deprecated and will be removed in Boost 1.87")
```

> Username: vissarion  
> Created_at: 2024-08-09 12:45:41 UTC  
> Updated_at: 2024-08-09 12:45:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1301#discussion_r1711399464  

Since this commit will be part of 1.87, should we change the message to 1.88?

> Username: barendgehrels  
> Created_at: 2024-08-20 06:25:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1301#discussion_r1722752430  

✅


---
