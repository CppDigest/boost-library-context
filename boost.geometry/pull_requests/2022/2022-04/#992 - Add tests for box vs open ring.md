# #992 [test][disjoint] Add tests for box vs open ring. [Merged]

> Username: awulkiew  
> Created at: 2022-04-12 11:23:47 UTC  
> Updated at: 2022-04-13 10:23:57 UTC  
> Merged at: 2022-04-13 10:23:54 UTC  
> Closed at: 2022-04-13 10:23:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/992  

Tests for issues: https://github.com/boostorg/geometry/issues/837 and https://github.com/boostorg/geometry/issues/982 after fix: https://github.com/boostorg/geometry/pull/990

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-04-12 11:28:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/992#pullrequestreview-939389507  

📁 test/algorithms/disjoint/disjoint_point_box_geometry.cpp

```diff
 106 |+     using mpolygon_ccw = bg::model::multi_polygon<polygon_ccw>;
 107 |+     
 108 |+     // https://github.com/boostorg/geometry/issues/837
```

> Username: mloskot  
> Created_at: 2022-04-12 11:28:27 UTC  
> Updated_at: 2022-04-12 11:28:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/992#discussion_r848326480  

I would quote issue `#XXX` number in commit message for easier lookup and metadata-integrity 😊

> Username: awulkiew  
> Created_at: 2022-04-12 11:30:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/992#discussion_r848328424  

Done. :)


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-04-12 14:11:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/992#pullrequestreview-939620243  

LGTM thanks!

---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-04-13 08:15:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/992#pullrequestreview-940586363  

📁 test/algorithms/disjoint/disjoint_point_box_geometry.cpp

```diff
 112 |+ 
 113 |+ 
 114 |+     // https://github.com/boostorg/geometry/issues/982
```

> Username: barendgehrels  
> Created_at: 2022-04-13 08:15:43 UTC  
> Updated_at: 2022-04-13 08:15:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/992#discussion_r849206225  

Personally I don't use the URL's. An `issue_xxx` is always a github "issue". For the old svn trac system I used `ticket_xxx` because they are called "tickets" there.  
  
https://github.com/boostorg/geometry/blob/develop/test/algorithms/overlay/multi_overlay_cases.hpp#L1500

> Username: awulkiew  
> Created_at: 2022-04-13 10:08:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/992#discussion_r849313005  

Ok, this information is indeed redundant.


---
