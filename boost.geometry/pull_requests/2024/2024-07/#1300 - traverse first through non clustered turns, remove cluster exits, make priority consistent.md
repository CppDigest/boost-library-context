# #1300 traverse first through non clustered turns, remove cluster exits, make priority consistent [Merged]

> Username: barendgehrels  
> Created at: 2024-07-30 18:22:48 UTC  
> Updated at: 2024-12-14 11:13:12 UTC  
> Merged at: 2024-08-08 13:24:59 UTC  
> Closed at: 2024-08-08 13:24:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1300  

Fixes issue #1293 #1294 and #1295  
  
This PR fixes several issues. There are no regressions.  
  
More detailed:  
* it changes traversal such that it first traverses non clustered turns (simpler) and only then the remaining clustered ones  
* that caused a regression in `issue_630_b` (a relatively simple case)  
* that was fixed by removing `cluster_exits` (a welcome removal, see below)  
* that caused three other regressions  
* they could be fixed by using the priority approach, earlier used only for union/buffer. It is now also used for intersection/difference and in exactly the same way  
  
Removing cluster_exits is welcome because it was designed as a simple approach, not needing many conditions. But in the end there are conditions there as well, and (apparently) bugs. So it is welcome to remove it again and keep the code having one approach, used for both union and intersection in the same way.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-07-30 18:23:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1300#pullrequestreview-2208383395  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 235 |+             ? select_source_generic<&segment_identifier::multi_index>(
 236 |+                         turn, candidate_seg_id, previous_seg_id)
 237 |+             : select_source_generic<&segment_identifier::source_index>(
```

> Username: barendgehrels  
> Created_at: 2024-07-30 18:23:21 UTC  
> Updated_at: 2024-07-30 18:23:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1300#discussion_r1697393581  

This is not part of the fix but makes code more concise and readable


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-07-30 18:24:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1300#pullrequestreview-2208384632  

📁 include/boost/geometry/algorithms/detail/overlay/traversal.hpp

```diff
 585 |                 // (opposite) direction, it can still be used.
 601 |-                 return cinfo.spike_count > 0 ? 1 : 0;
 586 |+                 return 1;
```

> Username: barendgehrels  
> Created_at: 2024-07-30 18:24:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1300#discussion_r1697394330  

Returning `0` was a bug, it should only have returned 1.  
So that is fixed and is necessary to avoid regressions with this PR


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2024-08-08 13:24:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1300#pullrequestreview-2227874114  

Thanks!

---
