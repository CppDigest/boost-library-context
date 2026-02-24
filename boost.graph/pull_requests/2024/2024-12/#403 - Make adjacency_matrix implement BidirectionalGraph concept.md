# #403 Make adjacency_matrix implement BidirectionalGraph concept [Merged]

> Username: Wavetrace  
> Created at: 2024-12-12 03:07:37 UTC  
> Updated at: 2025-03-04 18:52:02 UTC  
> Merged at: 2025-02-28 07:07:01 UTC  
> Closed at: 2025-02-28 07:07:01 UTC  
> Url: https://github.com/boostorg/graph/pull/403  

Adjacency matrix is by nature bidirectional and one can see that the concept was mostly implemented before. That implementation missed the degree function(s). These functions are implemented in this commit and the traversal_category is updated to meet the concept requirements.  
  
Tests are also added, though they are a bit shallow in the sense that they seem to omit intensive algorithms.  
  
This PR is a consequence of work on https://github.com/boostorg/graph/pull/397 -- that one contains a test commented out, that ends up running `neighbor_bfs` on an adjacency matrix.  
  
Fixes #404

---

## Review 1 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-02-25 09:40:58 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/403#pullrequestreview-2640326707  

📁 include/boost/graph/adjacency_matrix.hpp

```diff
 828 | }
 829 | 
 830 |+ // degree
```

> Username: jeremy-murphy  
> Created_at: 2025-02-25 09:37:32 UTC  
> Updated_at: 2025-02-25 09:40:58 UTC  
> Url: https://github.com/boostorg/graph/pull/403#discussion_r1969375416  

This comment is not doing much work. :)


---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-02-28 05:59:09 UTC  
> Url: https://github.com/boostorg/graph/pull/403#issuecomment-2689795347  

I thought it would be expedient to just fix the comments myself since the changes were trivial.

---

## Review 3 [Approved]

> Username: jeremy-murphy  
> Created_at: 2025-02-28 06:03:42 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/403#pullrequestreview-2649814087  

Thanks for a pragmatic missing piece of the puzzle!

---

## Comment 4

> Username: jonasliljenfeldt  
> Created_at: 2025-03-04 17:03:00 UTC  
> Url: https://github.com/boostorg/graph/pull/403#issuecomment-2698349269  

Nice one @Wavetrace!

---

## Comment 5

> Username: Wavetrace  
> Created_at: 2025-03-04 18:51:37 UTC  
> Updated_at: 2025-03-04 18:52:02 UTC  
> Url: https://github.com/boostorg/graph/pull/403#issuecomment-2698590407  

Thanks, Jonas! Thanks a lot, Jeremy, for fixing the comment!

---
