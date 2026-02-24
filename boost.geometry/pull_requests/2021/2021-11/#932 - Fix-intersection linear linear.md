# #932 Fix/intersection linear linear [Merged]

> Username: barendgehrels  
> Created at: 2021-11-03 14:34:14 UTC  
> Updated at: 2021-11-17 13:35:25 UTC  
> Merged at: 2021-11-17 10:21:54 UTC  
> Closed at: 2021-11-17 10:21:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/932  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-03 14:34:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/932#pullrequestreview-796641428  

📁 include/boost/geometry/algorithms/detail/overlay/follow_linear_linear.hpp

```diff
 340 |-             BOOST_THROW_EXCEPTION(inconsistent_turns_exception());
 339 |+            return oit;
 340 |         }
```

> Username: barendgehrels  
> Created_at: 2021-11-03 14:34:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#discussion_r742003482  

I don't think we should throw here.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-03 14:35:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/932#pullrequestreview-796642840  

📁 include/boost/geometry/core/config.hpp

```diff
  23 |+     #define BOOST_GEOMETRY_NO_ROBUSTNESS
  24 |+   #endif
  25 |+ #endif
```

> Username: barendgehrels  
> Created_at: 2021-11-03 14:35:39 UTC  
> Updated_at: 2021-11-03 14:35:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#discussion_r742004446  

This is because I add more and more tests which are "ready", and get the _alternate state now.  
As soon as we switch, they test the other way round because for a while we'll have to support old behavior too.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-03 14:36:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/932#pullrequestreview-796644378  

📁 test/algorithms/set_operations/intersection/intersection_linear_linear.cpp

```diff
1274 |- #ifdef BOOST_GEOMETRY_INTERSECTION_DO_NOT_INCLUDE_ISOLATED_POINTS
1275 |-          from_wkt<ML>("MULTILINESTRING((-0.7654 8.88178e-16,-0.7654 0,5 3))"),
1275 |+ #ifdef BOOST_GEOMETRY_NO_ROBUSTNESS
```

> Username: barendgehrels  
> Created_at: 2021-11-03 14:36:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#discussion_r742005595  

This result was actually better, in my opinion.  
  
It's this case.  
  
![mlmli21](https://user-images.githubusercontent.com/334849/140081003-66555373-5735-4a40-b69b-5e8b20b925c5.png)


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2021-11-03 14:37:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/932#pullrequestreview-796645938  

📁 test/algorithms/set_operations/intersection/test_intersection_linear_linear.hpp

```diff
  43 |+                          << " or: len=" << bg::length(mls_int2) << " count=" << bg::num_points(mls_int2)
  44 |+                          << " Detected: len=" << bg::length(mls_output) << " count=" << bg::num_points(mls_output)
  45 |+                          << " wkt=" << bg::wkt(mls_output)
```

> Username: barendgehrels  
> Created_at: 2021-11-03 14:37:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#discussion_r742006721  

In general, outputting wkt's in test failures is inconvenient.  
Some tests still do it, but it's often unreadable and therefore quite useless.


---

## Comment 5

> Username: barendgehrels  
> Created_at: 2021-11-17 08:23:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#issuecomment-971342760  

@awulkiew @vissarion could this PR be reviewed?

---

## Comment 6

> Username: awulkiew  
> Created_at: 2021-11-17 10:09:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#issuecomment-971427555  

@barendgehrels yes, sorry. LGTM.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2021-11-17 10:28:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/932#issuecomment-971442573  

Merged and closed. If there are still any concerns, they can be addressed.

---

## Review 8 [Commented]

> Username: vissarion  
> Created_at: 2021-11-17 13:35:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/932#pullrequestreview-808593757  

Sorry for being late here! I am OK.

---
