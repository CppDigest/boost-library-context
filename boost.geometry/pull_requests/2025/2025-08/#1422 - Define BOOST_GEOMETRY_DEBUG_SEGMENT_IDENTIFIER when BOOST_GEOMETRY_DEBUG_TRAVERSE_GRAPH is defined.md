# #1422 Define BOOST_GEOMETRY_DEBUG_SEGMENT_IDENTIFIER when BOOST_GEOMETRY_DEBUG_TRAVERSE_GRAPH is defined. [Merged]

> Username: tinko92  
> Created at: 2025-08-01 13:59:05 UTC  
> Updated at: 2025-08-02 05:40:47 UTC  
> Merged at: 2025-08-01 18:20:27 UTC  
> Closed at: 2025-08-01 18:20:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1422  

A trivial change because it is not possible to compile with BOOST_GEOMETRY_DEBUG_TRAVERSE_GRAPH, which requires the output of seg_ids, without BOOST_GEOMETRY_DEBUG_SEGMENT_IDENTIFIER.

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2025-08-01 16:37:17 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1422#pullrequestreview-3079884311  

Thanks

---
