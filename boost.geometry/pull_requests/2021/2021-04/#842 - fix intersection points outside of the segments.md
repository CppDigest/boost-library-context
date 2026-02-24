# #842 [intersection] fix intersection points outside of the segments [Merged]

> Username: barendgehrels  
> Created at: 2021-04-14 12:40:09 UTC  
> Updated at: 2021-04-28 08:31:28 UTC  
> Merged at: 2021-04-28 08:31:22 UTC  
> Closed at: 2021-04-28 08:31:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/842  

This fixes bug #838 (the visible artifact was an intersection point far away from both segments)  
  
It also fixes some other errors (probably artifacts)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-04-14 12:42:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/842#pullrequestreview-635570977  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 324 |+             {
 325 |+                 geometry::set<0>(point, geometry::get<0, 0>(a));
 326 |+                 geometry::set<1>(point, geometry::get<0, 1>(a));
```

> Username: barendgehrels  
> Created_at: 2021-04-14 12:42:19 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r613211405  

I'll probably change this to `assign` or something we have for this

> Username: barendgehrels  
> Created_at: 2021-04-21 11:02:50 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r617434263  

:heavy_check_mark: done


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2021-04-14 12:42:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/842#pullrequestreview-635571117  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 300 |             }
 301 |+ 
 302 |+             // Verify
```

> Username: barendgehrels  
> Created_at: 2021-04-14 12:42:30 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r613211505  

Will remove

> Username: barendgehrels  
> Created_at: 2021-04-21 11:03:01 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r617434375  

:heavy_check_mark:


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2021-04-14 12:45:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/842#pullrequestreview-635574246  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 258 |+                     && geometry::get<1>(point) < geometry::get<Index, 1>(s))
 259 |+                 || (geometry::get<Index, 1>(s) > geometry::get<1 - Index, 1>(s)
 260 |+                     && geometry::get<1>(point) > geometry::get<Index, 1>(s));
```

> Username: barendgehrels  
> Created_at: 2021-04-14 12:45:45 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r613213853  

I can make an extra function per dimension such that will look simpler, and add some ascii art

> Username: barendgehrels  
> Created_at: 2021-04-21 11:03:12 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r617434485  

:heavy_check_mark: done


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-04-27 21:42:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/842#pullrequestreview-646383810  

📁 include/boost/geometry/policies/robustness/segment_ratio.hpp

```diff
 113 |+ 
 114 |+ // Any ratio based on non-floating point (integer or arithmetic)
 115 |+ // will not be collinear.
```

> Username: awulkiew  
> Created_at: 2021-04-27 21:42:15 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r621621761  

What do you mean by arithmetic? Do you mean user-defined types? They could represent FP number right?  
  
Also why integers won't be possibly collinear? I mean for them possibly collinear and strictly collinear would be the same, right? My point is I don't understand why does this specialization return always `false`.

> Username: barendgehrels  
> Created_at: 2021-04-28 07:47:23 UTC  
> Updated_at: 2021-04-28 08:16:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r621902831  

Good point I will have a look

> Username: barendgehrels  
> Created_at: 2021-04-28 08:17:43 UTC  
> Updated_at: 2021-04-28 08:17:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/842#discussion_r621926349  

:heavy_check_mark: I changed it. Thanks. I will merge it because next PR depends on it.


---
