# #287 Change the range insertion return type match the documentation [Merged]

> Username: k3DW  
> Created at: 2024-09-26 04:02:32 UTC  
> Updated at: 2024-09-28 03:24:11 UTC  
> Merged at: 2024-09-28 03:24:08 UTC  
> Closed at: 2024-09-28 03:24:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/287  

Closes #284

---

## Review 1 [Commented]

> Username: joaquintides  
> Created_at: 2024-09-26 20:11:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/unordered/pull/287#pullrequestreview-2332259260  

📁 include/boost/unordered/concurrent_flat_map.hpp

```diff
 478 |         BOOST_UNORDERED_STATIC_ASSERT_INVOCABLE(F)
 477 |-         for (; first != last; ++first) {
 479 |+         size_type count = 0;
```

> Username: joaquintides  
> Created_at: 2024-09-26 20:11:05 UTC  
> Url: https://github.com/boostorg/unordered/pull/287#discussion_r1777678724  

GCC 7 complains that this variable shadows `count` member function. Happens passim.  
  
https://github.com/boostorg/unordered/actions/runs/11045334436/job/30682893588?pr=287


---

## Review 2 [Approved]

> Username: joaquintides  
> Created_at: 2024-09-27 08:16:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/unordered/pull/287#pullrequestreview-2333081422  

LGTM. There are some shadowing and conversion warnings in Boost.Interprocess, not our fault:  
  
https://drone.cpp.al/boostorg/unordered/1377/1/2  
https://drone.cpp.al/boostorg/unordered/1377/2/2  
https://drone.cpp.al/boostorg/unordered/1377/3/2  
  
Would you mind filing issues there? Thank you!

---
