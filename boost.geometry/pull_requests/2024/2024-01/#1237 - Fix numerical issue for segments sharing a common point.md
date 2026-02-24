# #1237 Fix numerical issue for segments sharing a common point [Merged]

> Username: vissarion  
> Created at: 2024-01-31 15:05:24 UTC  
> Updated at: 2024-04-01 11:29:35 UTC  
> Merged at: 2024-02-26 09:42:14 UTC  
> Closed at: 2024-02-26 09:42:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237  

Fixes https://github.com/boostorg/geometry/issues/1230

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-10 12:16:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1873632255  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 460 |         bool collinear = sides.collinear();
 461 | 
 462 |+         //TODO: remove this
```

> Username: barendgehrels  
> Created_at: 2024-02-10 12:16:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485086388  

In this PR?

> Username: vissarion  
> Created_at: 2024-02-12 09:23:55 UTC  
> Updated_at: 2024-02-12 09:23:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485911275  

ideally in the PR that removes rescaling (I added a note)


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-10 12:19:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1873632572  

📁 test/algorithms/Jamfile

```diff
  43 |-     [ run is_valid_failure.cpp         : : : <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_is_valid_failure_alternative ]
  44 |-     [ run is_valid_geo.cpp             : : : <define>BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE : algorithms_is_valid_geo_alternative ]
  42 |     [ run line_interpolate.cpp         : : : : algorithms_line_interpolate ]
```

> Username: barendgehrels  
> Created_at: 2024-02-10 12:19:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485087370  

In general, I think we should remove these in a separate PR.  
Now it looks just like it's done because the change still gives errors in rescaling code.  
By chance, I added all these in the cmake PR - but if it is already removed, that part is neither necessary there anymore (or it will break after merging this PR).  
  
Can you please split it?

> Username: vissarion  
> Created_at: 2024-02-12 09:25:18 UTC  
> Updated_at: 2024-02-12 09:25:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485912824  

I need this is this PR because there tests are failing with the new fix. I will place them in a different commit.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-10 12:21:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1873632719  

📁 test/algorithms/is_valid.cpp

```diff
1436 |     multi_linestring_type mls;
1443 |-     bg::intersection(ls1, ls2, mls);
1437 |+     bg::read_wkt("MULTILINESTRING((nan nan))", mls);
```

> Username: barendgehrels  
> Created_at: 2024-02-10 12:21:14 UTC  
> Updated_at: 2024-02-10 12:21:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485088042  

Can these changes be explained?

> Username: vissarion  
> Created_at: 2024-02-12 09:31:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485919536  

To my understanding we want to test the validity of a geometry with NaN coordinates. So I explicitly created one here instead of creating one as as result of an (unstable) intersection.   
  
Side question, why do we need such an intersection to return NaN? Is it a desired feature or a numerical error that was used here to test NaN coordinates?

> Username: barendgehrels  
> Created_at: 2024-02-12 17:23:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486525773  

I think the last.  
I didn't add these cases here, I think @awulkiew  did.  
Probably for the reason that the intersection indeed returned something like that, and then the `is_valid` functionality should check these results.

> Username: awulkiew  
> Created_at: 2024-02-25 12:26:31 UTC  
> Updated_at: 2024-02-25 12:31:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1501810625  

It was added by @mkaravel:  
https://github.com/boostorg/geometry/commit/73960f31ca3e6483a5846bbf0b6ad601a4640fbe  
It seems such geometry was causing infinite loops:  
https://github.com/boostorg/geometry/commit/ee906b2f40a10f27b4a44b7396a611323310f2ce  
I think we should leave this test.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-10 12:22:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1873632821  

📁 test/algorithms/set_operations/intersection/intersection_linear_linear.cpp

```diff
1369 |                       (7.08745 -329.0674155),(5.06428 -559.024344),\
1370 |                       (3.23365 -767.0972558),(3.16036 -775.427199),\
1371 |-                       (-0.7654 8.88178e-16,-0.7654 0,5 3))"),
```

> Username: barendgehrels  
> Created_at: 2024-02-10 12:22:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485088294  

So we don't test these degenerate cases anymore?  
I'm not against it, but I wonder why - it breaks the new code? Why?

> Username: vissarion  
> Created_at: 2024-02-12 13:27:13 UTC  
> Updated_at: 2024-02-12 13:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486179309  

This is the result of the intersection (the inputs are a few lines above and they are not changed). So the results here are a bit different with the new code. Actually, they look better since they create multilinestring without the almost duplicate points (-0.7654 8.88178e-16) and (-0.7654 0) that is created since (-0.7654 0) is contained in both input multilinstrings.

> Username: barendgehrels  
> Created_at: 2024-02-12 17:23:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486526515  

Okay, that looks fine to me. Thanks!


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2024-02-10 12:23:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1873632932  

📁 include/boost/geometry/policies/relate/intersection_points.hpp

```diff
  72 |+         boost::geometry::assign(result.intersections[0], p);
  73 |+ 
  74 |+         // Temporary - this should go later
```

> Username: barendgehrels  
> Created_at: 2024-02-10 12:23:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485088591  

When will this go? In this PR?

> Username: vissarion  
> Created_at: 2024-02-12 13:30:08 UTC  
> Updated_at: 2024-02-12 13:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486182482  

This is copy paste from the segments_crosses() function above. I do not understand it either, but without that line the results are not correct, thus the information assigned to fractions is useful and cannot go now.

> Username: vissarion  
> Created_at: 2024-02-12 13:30:53 UTC  
> Updated_at: 2024-02-12 13:31:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486183960  

Do you have any idea of the reasoning behind the original comment?

> Username: barendgehrels  
> Created_at: 2024-02-12 17:24:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1486527812  

Actually, the whole fractions could be revised / simplified if rescaling goes.  
It might be that the comment is related to that.


---

## Review 6 [Commented]

> Username: vissarion  
> Created_at: 2024-02-12 09:31:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1874820719  

📁 test/algorithms/is_simple.cpp

```diff
 266 |     multi_linestring_type mls;
 273 |-     bg::intersection(ls1, ls2, mls);
 267 |+     bg::read_wkt("MULTILINESTRING((nan nan))", mls);
```

> Username: vissarion  
> Created_at: 2024-02-12 09:31:41 UTC  
> Updated_at: 2024-02-12 09:31:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#discussion_r1485920002  

This is the same as the `is_valid` case.


---

## Review 7 [Approved]

> Username: barendgehrels  
> Created_at: 2024-02-12 17:30:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1237#pullrequestreview-1875803573  

Thanks for handling my comments.  
  
I still don't understand all the details of the change itself (though it's not that much), but I think it's fine, and if all tests pass (we have so many, fortunately) - it should be completely fine.

---

## Comment 8

> Username: vissarion  
> Created_at: 2024-02-13 09:42:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1237#issuecomment-1940944655  

I should switch back to writing a short intro to my PRs :)  
  
Let me try to briefly explain the issue and the solution now.   
  
The intersection of two segments is computed here   
https://github.com/boostorg/geometry/pull/1237/files#diff-bad1f35b9d58f7f24c666340327c6ac0fa1ac0ffb5b2ae59fa905e15699c0550R547  
  
For the case of two segments with a common edge point (vertex) we apriori know the intersection and we do not have to compute it. Thus we baypass the segment intersection here:  
https://github.com/boostorg/geometry/pull/1237/files#diff-bad1f35b9d58f7f24c666340327c6ac0fa1ac0ffb5b2ae59fa905e15699c0550R537  
  
This is faster but more importantly more accurate we explicitly return the common point instead of computing the intersection which is sensitive to numerical inaccuracies.

---
