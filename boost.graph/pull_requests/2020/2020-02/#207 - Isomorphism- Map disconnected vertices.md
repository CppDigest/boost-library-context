# #207 Isomorphism: Map disconnected vertices [Merged]

> Username: jan-grimo  
> Created at: 2020-02-16 10:50:47 UTC  
> Updated at: 2023-09-04 03:00:53 UTC  
> Merged at: 2023-09-04 03:00:16 UTC  
> Closed at: 2023-09-04 03:00:16 UTC  
> Url: https://github.com/boostorg/graph/pull/207  

Isomorphism: Map disconnected vertices  
- Adds O(V) algorithm at `return_point_true` to match disconnected  
  vertices in the isomorphism map  
- Adds colored isomorphism test ensuring no null vertices are in the  
  isomorphism map  
  
Related to #203

---

## Comment 1

> Username: joemalle  
> Created_at: 2021-06-13 02:29:10 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-860141073  

Fixes #263

---

## Comment 2

> Username: jan-grimo  
> Created_at: 2021-06-14 09:19:38 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-860534651  

If #205 is merged before this, then this MR needs to be updated with removing the documentation addition noted there regarding possible null vertices in isomorphism maps

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2023-07-07 00:33:51 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1624473286  

> If #205 is merged before this, then this MR needs to be updated with removing the documentation addition noted there regarding possible null vertices in isomorphism maps  
  
Ahh, haha, I just merged #205 , so yes, sorry, we'll have to do that.

---

## Review 4 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:29:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/207#pullrequestreview-1517593639  

📁 include/boost/graph/isomorphism.hpp

```diff
 380 |+                         typedef unordered_multimap< invar2_value, vertex2_t > g2_invar_vertex_multimap;
 381 |+                         typedef typename g2_invar_vertex_multimap::iterator multimap_iter;
 382 |+                         g2_invar_vertex_multimap unmatched_invar_multimap;
```

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:29:49 UTC  
> Updated_at: 2023-07-07 01:31:06 UTC  
> Url: https://github.com/boostorg/graph/pull/207#discussion_r1255098798  

Please try not to repeat the type in the variable name, so in this case please remove the `_multimap` suffix.  
  
Also, `invar` is a confusing contraction -- just use the full word `invariant(s)` in all cases.


---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2023-07-07 01:38:04 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1624513527  

@joemalle , are you able to review this code too? It looks good to me, but it sounds like you have some experience with this functionality of the library, so your opinion would be appreciated.

---

## Comment 6

> Username: joemalle  
> Created_at: 2023-07-07 16:06:28 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1625632102  

I'm sorry, but I cannot help with this one.  I ran into this issue several years ago but haven't thought about it since.  The test looks convincing at the very least

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2023-07-24 00:34:54 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1647028622  

@jargonzombies , did you need to make more changes here since the other PRs are merged or is this ready to go?

---

## Comment 8

> Username: jan-grimo  
> Created_at: 2023-07-24 09:12:23 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1647523106  

This is ready to go

---

## Review 9 [Commented]

> Username: jeremy-murphy  
> Created_at: 2023-08-01 00:40:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/graph/pull/207#pullrequestreview-1556003924  

📁 include/boost/graph/isomorphism.hpp

```diff
 402 |+                     }
 403 |+                     return true;
 404 |+                 }
```

> Username: jeremy-murphy  
> Created_at: 2023-08-01 00:40:38 UTC  
> Updated_at: 2023-08-01 00:40:39 UTC  
> Url: https://github.com/boostorg/graph/pull/207#discussion_r1280006353  

This `match` function is already huge, right, so I would prefer not to make it huger. Could you just put this bit of functionality into a private member function, please?  
  
If you can abstract it to be a worthwhile non-member, generic function, that would be even better, but that could be a lot of work. Private member function would be a perfectly fine first step.


---

## Comment 10

> Username: jeremy-murphy  
> Created_at: 2023-09-04 03:00:53 UTC  
> Url: https://github.com/boostorg/graph/pull/207#issuecomment-1704543497  

Thanks for the contribution and apologies again for the long delay getting it merged!

---
