# #115 Fix G++8 warnings: catch by reference and unused variables [Merged]

> Username: pavelkryukov  
> Created at: 2018-09-02 11:11:42 UTC  
> Updated at: 2018-10-17 17:39:00 UTC  
> Merged at: 2018-10-17 17:38:50 UTC  
> Closed at: 2018-10-17 17:38:50 UTC  
> Url: https://github.com/boostorg/graph/pull/115  



---

## Comment 1

> Username: anadon  
> Created_at: 2018-09-02 14:14:23 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-417933794  

I'll add this to the second batch of merges.  The first one is turning out rather large while I fix up build errors and I absolutely do not want anything larger.

---

## Comment 2

> Username: pavelkryukov  
> Created_at: 2018-09-02 15:18:08 UTC  
> Updated_at: 2018-09-02 15:18:27 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-417937946  

These changes _**are**_ the build fixes.

---

## Comment 3

> Username: pavelkryukov  
> Created_at: 2018-09-02 22:35:03 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-417964592  

GCC 8 build is blocked by https://github.com/boostorg/serialization/pull/84

---

## Comment 4

> Username: anadon  
> Created_at: 2018-10-12 19:51:06 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-429443341  

Can you please rebase your PR on the current devel branch?  It should clean up your build errors.

---

## Comment 5

> Username: pavelkryukov  
> Created_at: 2018-10-12 20:12:41 UTC  
> Updated_at: 2018-10-12 21:20:55 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-429448920  

Done.  
I kept a `/* weight_type w = */` comment since `w` is used in other comments.

---

## Review 6 [Commented]

> Username: anadon  
> Created_at: 2018-10-15 18:52:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/115#pullrequestreview-164847208  

📁 include/boost/graph/maximum_adjacency_search.hpp

```diff
 151 |         const vertex_descriptor u = pq.top(); // u = extractmax(PQ)
 152 |-         get(keys, u);                        vis.start_vertex(u, g);
 152 |+         /* weight_type w = */ get(keys, u);      vis.start_vertex(u, g);
```

> Username: anadon  
> Created_at: 2018-10-15 18:52:43 UTC  
> Url: https://github.com/boostorg/graph/pull/115#discussion_r225278260  

Should this comment still be here like this?

> Username: pavelkryukov  
> Created_at: 2018-10-15 18:53:43 UTC  
> Updated_at: 2018-10-15 18:53:44 UTC  
> Url: https://github.com/boostorg/graph/pull/115#discussion_r225278591  

See my comment above.


---

## Comment 7

> Username: jzmaddock  
> Created_at: 2018-10-16 17:18:06 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-430322026  

This all looks good to go to me - anyone else?

---

## Comment 8

> Username: anadon  
> Created_at: 2018-10-16 18:22:48 UTC  
> Url: https://github.com/boostorg/graph/pull/115#issuecomment-430344735  

It looks OK to me.  Still need to brush up on some of the stdlib use here, but seems good.

---
