# #1113 [union] fix missing interior ring and double traversed exterior ring [Merged]

> Username: barendgehrels  
> Created at: 2023-02-15 15:06:44 UTC  
> Updated at: 2024-12-14 11:14:11 UTC  
> Merged at: 2023-03-07 15:23:34 UTC  
> Closed at: 2023-03-07 15:23:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113  

fixes: #1109 and #1108  
keeps fixed: #1081

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2023-02-17 13:52:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1113#pullrequestreview-1303451051  

Thanks, I am OK with merging!

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 108 |+         using point_t = decltype(turns[*indices.begin()].point);
 109 |+         using coor_t = typename coordinate_type<point_t>::type;
 110 |+         coor_t centroid_0{};
```

> Username: vissarion  
> Created_at: 2023-02-17 13:41:44 UTC  
> Updated_at: 2023-02-17 13:52:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1109826925  

I do not remember using this initialization in boost geometry. I am not against though, just to check.

> Username: awulkiew  
> Created_at: 2023-02-18 19:23:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1111100921  

I'd prefer to assign `0` explicitly because for user-defined numerical type this could result in default ctor call which could initialize variable to something different than `0`.

> Username: barendgehrels  
> Created_at: 2023-02-22 09:36:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114066635  

I will update it. Thanks.

> Username: barendgehrels  
> Created_at: 2023-02-22 12:26:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114253948  

:heavy_check_mark:

---

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
  81 |+ // It is necessary to avoid artefacts and invalidities.
  82 |+ template <typename Turns, typename Clusters>
  83 |+ inline void colocate_clusters(Turns& turns, Clusters const& clusters)
```

> Username: vissarion  
> Created_at: 2023-02-17 13:42:34 UTC  
> Updated_at: 2023-02-17 13:52:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1109827964  

Shouldn't the order be the other way around? Such that the output is after then input?

> Username: barendgehrels  
> Created_at: 2023-02-22 09:37:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114066878  

You are right will fix it.

> Username: barendgehrels  
> Created_at: 2023-02-22 12:26:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114254061  

:heavy_check_mark:


📁 test/algorithms/buffer/buffer_multi_polygon.cpp

```diff
 542 |     test_one<multi_polygon_type, polygon_type>("rt_p12", rt_p12, join_miter, end_flat, 22.5711, 1.0);
 543 |+ 
 544 |+     // Needs centroid of cluster turn points
```

> Username: vissarion  
> Created_at: 2023-02-17 13:48:47 UTC  
> Updated_at: 2023-02-17 13:52:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1109835580  

this is related to flag BOOST_GEOMETRY_USE_FIRST_POINT_AT_CLUSTER

> Username: barendgehrels  
> Created_at: 2023-02-22 12:29:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114257525  

This define is now gone.  
:heavy_check_mark:


📁 test/algorithms/overlay/get_clusters.cpp

```diff
 106 |-     double const multiplier = 1.0 / 1000.0;
 105 |+     // which is now return T(100) (earlier it was 1000)
 106 |+     double const multiplier = 1.0 / 10.0;
```

> Username: vissarion  
> Created_at: 2023-02-17 13:52:03 UTC  
> Updated_at: 2023-02-17 13:52:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1109839744  

does it hold `multiplier = T / 1000.0;` ?

> Username: barendgehrels  
> Created_at: 2023-02-22 09:37:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114068049  

I don't understand. Maybe the comment needs to point to the source in get_clusters.hpp

> Username: vissarion  
> Created_at: 2023-02-22 09:53:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114086968  

My question is: "should `multiplier` be a function of `T` e.g.  `multiplier = T / 1000.0;` instead of manually update the fraction every time you change the threshold?" Am I missing something in the context here?

> Username: barendgehrels  
> Created_at: 2023-02-22 12:29:22 UTC  
> Updated_at: 2023-02-22 12:29:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114257125  

Changed these lines a bit, avoiding the multiplier and enhancing the comment  
:heavy_check_mark:

> Username: barendgehrels  
> Created_at: 2023-02-22 13:29:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114328334  

> My question is: "should `multiplier` be a function of `T` e.g. `multiplier = T / 1000.0;` instead of manually update the fraction every time you change the threshold?" Am I missing something in the context here?  
  
Sorry, I noticed this answer only after my own reply.  
But I changed it, without multiplier now. I hope the threshold won't change again. Is it fine like this?


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2023-02-18 19:28:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1113#pullrequestreview-1304659344  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
 116 |+         }
 117 |+         centroid_0 /= indices.size();
 118 |+         centroid_1 /= indices.size();
```

> Username: awulkiew  
> Created_at: 2023-02-18 19:28:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1111101377  

AFAIU this is only correct for cartesian. Can this be a problem?  
  
Another issue is that for integral coordinates this could result in overflow.

> Username: barendgehrels  
> Created_at: 2023-02-22 09:41:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114071968  

Good points.  
Happening on the dateline would be a rare case... but, see below.  
Integral coordinates: if they are clustered, they are (by definition) on the same location. So they don't need this, the first point is fine.  
I'll update the implementation to cover these two situations (which means: for geographic or integer coordinates take the first).

> Username: barendgehrels  
> Created_at: 2023-02-22 12:26:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114254280  

:heavy_check_mark:

> Username: barendgehrels  
> Created_at: 2023-02-22 14:13:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114382204  

So indeed it was a problem, reported as #1116 which was found on this branch.  
Good catch!  
My updated implementation solved that already, but I will create a testcase.  
That will be done in a separate PR. If you approve, this can be merged now.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2023-02-22 12:30:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1113#pullrequestreview-1309140949  

📁 include/boost/geometry/algorithms/detail/overlay/colocate_clusters.hpp

```diff
  50 |+         {
  51 |+             turns[*it].point = first_point;
  52 |+         }
```

> Username: barendgehrels  
> Created_at: 2023-02-22 12:30:39 UTC  
> Updated_at: 2023-02-22 12:30:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114258418  

Because all implementation is here, I removed the define, better like this.  
Thanks for the comments.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2023-02-22 12:31:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1113#pullrequestreview-1309142043  

📁 include/boost/geometry/algorithms/detail/overlay/handle_colocations.hpp

```diff
  98 | 
  99 |     remove_clusters(turns, clusters);
 100 |+     colocate_clusters(clusters, turns);
```

> Username: barendgehrels  
> Created_at: 2023-02-22 12:31:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#discussion_r1114259197  

This now looks a bit weird (by the order change).  
I will change it in a later PR but I didn't want to do it in this one, to keep the size smaller.


---

## Comment 5

> Username: vissarion  
> Created_at: 2023-03-06 08:49:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1113#issuecomment-1455720810  

@barendgehrels do you want to have this PR in 1.82 beta? Deadline 8/3  
  
*Side note:* there is an error in buffer tests https://app.circleci.com/pipelines/github/boostorg/geometry/374/workflows/e4f969a0-4b45-4af2-8316-328437fde513/jobs/19175 probably not related to this PR.

---
