# #1343 Fix: distinguish turn to discard in case of start turns [Merged]

> Username: barendgehrels  
> Created at: 2024-11-16 09:14:26 UTC  
> Updated at: 2024-11-18 17:09:55 UTC  
> Merged at: 2024-11-18 17:09:51 UTC  
> Closed at: 2024-11-18 17:09:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1343  

Fixes #1342

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-11-16 09:15:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1343#pullrequestreview-2440335548  

📁 include/boost/geometry/algorithms/detail/overlay/discard_duplicate_turns.hpp

```diff
 129 |+                     // Discard the start turn, unless there is a touch before.
 130 |+                     // In that case the start is used and the touch is discarded.
 131 |+                     (is_touch ? turn : start_turn).discarded = true;
```

> Username: barendgehrels  
> Created_at: 2024-11-16 09:15:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1343#discussion_r1844942463  

▶️ This is the fix

> Username: vissarion  
> Created_at: 2024-11-18 16:33:29 UTC  
> Updated_at: 2024-11-18 16:33:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1343#discussion_r1846909478  

Similar to https://github.com/boostorg/geometry/pull/1341 this is again related to start turns and how to treat them in special cases.

> Username: barendgehrels  
> Created_at: 2024-11-18 17:09:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1343#discussion_r1846962830  

Indeed. It's quite related. I want to harmonize it at some point.  
That one was about touch/touch_interior  
This one is about touch/start ...  
  
I want to build the map (segment -> turnindex) once for both options and then inspect.  
But last week, this was not possible because the usecase is slightly different.  
  
I'm trying to inspect it more and get related cases and refactor. But that will  be `1.88` I think.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-11-18 16:33:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1343#pullrequestreview-2443146606  

Thanks for the fix!

---
